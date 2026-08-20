---
name: orchestra-luna
description: Codex のネイティブ subagent workflow を使って、複数の GPT-5.6 Luna エージェントに独立に考えさせ、別の Luna エージェントで反証・前提監査を行ってから親エージェントが統合する。最初に見つかった答えを正解扱いしない。調査、設計、デバッグ、レビュー、実装計画など、品質を上げたいタスクで使う。
---

# orchestra-luna

今回の依頼: $ARGUMENTS

あなたは指揮者。Codex の **native subagents** を使う。外部 CLI を別プロセスで大量起動する方式は使わない。
独立に分けられる仕事は subagent に委譲し、原則として子エージェントは **gpt-5.6-luna** を明示指定する。

この skill の最重要ルール:

> **最初に見つかった答えは「正解」ではなく Candidate 0 である。**
> 反証フェーズを通過するまで、採用・実装・最終回答を確定してはいけない。

詳細な反証規律は [references/anti-first-answer.md](references/anti-first-answer.md) を読む。

## 1. 基本配役

子エージェントは原則 Luna。タスクごとに明示的にモデルと reasoning effort を指定する。

| 役割 | モデル | effort | 原則 |
|---|---|---:|---|
| evidence / explorer | gpt-5.6-luna | medium | 原文・実コード・ログから事実収集。提案しない |
| candidate A/B/C | gpt-5.6-luna | high | 互いに独立。互いの案を見せない |
| falsifier | gpt-5.6-luna | high | 最有力案を壊す。褒めない |
| assumption auditor | gpt-5.6-luna | high | 全案に共通する未検証前提を探す |
| tie-breaker | gpt-5.6-luna | high | 実質的な不一致が残った時だけ使う |
| implementation worker | gpt-5.6-luna | high | 確定した計画だけを単独で実装する |

親エージェントは編成、受入条件、最終統合、ユーザーへの回答を担当する。
子エージェントにさらに子を作らせる必要は通常ない。深さ 1 を基本とする。

## 2. effort

ユーザー指定があればそれを優先する。

- `--light`: Candidate 2 + Falsifier 1
- `--standard`: Evidence 1 + Candidate 3 + Falsifier 1 + Assumption Auditor 1
- `--deep`: Evidence 2 + Candidate 4 + Falsifier 2 + Assumption Auditor 1。必要なら Tie-breaker 1

指定がない場合:
- 単純な局所質問 = light
- 調査、設計、デバッグ、レビュー = standard
- 大規模変更、不可逆、セキュリティ、根本原因不明、複数コンポーネント = deep

## 3. Phase A — Contract

親が最初に次を短く固定する。

1. ユーザーの目的
2. 成功条件 3〜7 個
3. やってはいけないこと
4. 未知の点
5. effort

ここでは答えを決めない。

## 4. Phase B — Independent search

### 4.1 Evidence

standard/deep では evidence agent を先に並列起動する。
持ち場を重複させすぎない。例:
- 実コード / 呼び出し経路
- テスト / 設定 / ドキュメント
- ログ / 再現条件

Evidence agent には「解決策を提案するな」と明示する。

### 4.2 Candidates

Candidate agent を **同時に** 起動する。全員に同じ成功条件を渡すが、他 candidate の答えは渡さない。

各 candidate に必ず要求する:
- 結論
- 根拠
- 未検証の前提
- 失敗しうるケース
- 自分の案を否定するには何を確認すべきか

レンズ例:
- A: 最小変更 / 最短経路
- B: 根本原因 / 原理から再構成
- C: 反対仮説 / 別原因を前提に考える
- D: 運用・将来変更・障害耐性 (deep)

**禁止:** Candidate 0 が早く返ってきても、その時点で結論を作らない。計画した candidate が揃うまで待つ。

## 5. Phase C — Falsification gate

候補が揃ったら、初めて反証側を起動する。

### Falsifier
最有力候補を渡し、次を要求する。
- その結論が間違っていると仮定して壊す
- 具体的な反例、エッジケース、逆の証拠を探す
- 重要主張を evidence と照合する
- 証拠のない懸念は「未証明」と分ける
- `PASS / REVISE / REJECT` のどれかを返す

### Assumption Auditor
全候補の要約を渡し、次を要求する。
- 全候補が暗黙に共有している前提
- その前提が崩れた時に結論が反転する条件
- 候補に存在しない代替仮説
- evidence で未確認の重要点

この 2 役は Candidate の作者と同じ thread にしない。

## 6. Phase D — Adjudication

親が次の順で統合する。

1. 成功条件を満たさない案を落とす
2. falsifier が `REJECT` した案は、反証を潰す証拠がない限り落とす
3. `REVISE` は最小修正で直せるか確認する
4. 全案共通前提に未検証の重大項目があれば、必要な追加調査を 1 回だけ走らせる
5. それでも A/B が実質的に割れる場合だけ tie-breaker を起動する
6. 採用案と、捨てた案を捨てた理由を親が決定する

多数決は禁止。**証拠と受入条件で決める。**

## 7. 実装タスク

並列書き込みは原則禁止。サブエージェント公式の推奨どおり、読み取り・探索・レビューは並列、書き込みは単独所有に寄せる。

実装フロー:

1. Phase A〜D で計画を確定
2. implementation worker 1 名だけに変更を担当させる
3. テスト / lint / typecheck / build のうち repo にあるものを実行
4. 変更後に **新しい** falsifier と reviewer を並列起動
5. blocker があれば同じ implementation worker が修正
6. 再検証してから完了

同じファイルを複数 worker に同時編集させない。
worktree を必須にしない。repo の規約で禁止されているなら絶対に作らない。

## 8. 調査・Q&A

調査でも最初の検索結果を正解扱いしない。

- Evidence: 一次情報 / 原文 / 現物を優先
- Candidate: 少なくとも 2 つの説明・仮説を独立生成
- Falsifier: 有力説明に反する情報を探す
- 最終回答: 確定 / かなり有力 / 未確定 を区別する

事実が時点依存なら web / docs 等の利用可能な最新情報源で確認する。

## 9. 出力規律

subagent の生ログを親スレッドへ大量コピーしない。各 agent からは次だけを戻させる。

- finding / proposal
- evidence (file:line, command output, URL 等)
- uncertainty
- recommended next action

最終回答では通常、内部の全 agent の会話を列挙しない。ユーザーの意思決定に必要なら:
- 採用結論
- 決め手
- 反証で確認したこと
- 残る不確実性
を簡潔に示す。

## 10. Stop condition

次を満たしたら終了してよい。

- 成功条件を満たす案がある
- 重要主張に証拠がある
- falsifier に未解決 blocker がない
- 共有前提の重大な未検証がない、または不確実性として明示できる

同じ論点を新しい言い換えだけで反復しない。新しい証拠・反例が出ないなら収束とみなす。
