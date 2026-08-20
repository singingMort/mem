# Anti-first-answer protocol

## Purpose

LLM は最初に得たもっともらしい仮説へアンカリングしやすい。この skill では「最初の回答を改善する」のではなく、**最初の回答を候補の一つへ格下げする**。

## Hard rules

1. **First answer = Candidate 0.** 採用案ではない。
2. Candidate 同士は独立。先行案を後続案へ見せない。
3. 反証者は Candidate の作者と分離する。
4. 「別案を考えて」だけでは不十分。レンズを変える。
5. 多数決ではなく、証拠・再現・受入条件で裁定する。
6. 全案が一致した場合ほど、共有前提を疑う。
7. 反証が見つからなかったことと、真であることを混同しない。

## Candidate prompt skeleton

- Solve the task independently.
- Do not assume an earlier or obvious answer is correct.
- State the strongest answer you can support.
- Separate facts from assumptions.
- Name at least one plausible alternative explanation.
- State what evidence would falsify your answer.
- Return concise evidence-backed results only.

## Falsifier prompt skeleton

- Assume the current leading answer is wrong.
- Try to falsify it, not improve its presentation.
- Look for counterexamples, edge cases, contradictory evidence, hidden assumptions, and tests that would fail.
- Distinguish evidenced defects from speculative concerns.
- Return PASS, REVISE, or REJECT with evidence.

## Assumption-auditor prompt skeleton

- Compare all candidate answers.
- Identify assumptions shared by all of them.
- Find missing hypotheses that no candidate considered.
- Identify what fact, if changed, would reverse the decision.
- Flag important claims that were copied from each other or are unsupported by primary evidence.

## Common failure modes

- Sequential anchoring: Candidate B receives Candidate A and merely edits it.
- Premature synthesis: Parent begins merging after the first fast response.
- Critic theater: Critic is told to "review" but not to seek falsification.
- Fake diversity: Three agents use the same lens and produce wording variants.
- Majority voting: 3/4 agents agree, so parent declares truth without evidence.
- Endless critique: New critics keep inventing stylistic nits. Stop when no new material evidence appears.
