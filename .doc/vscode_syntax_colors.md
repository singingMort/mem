# VS Code 構文色設定

抽出元: [`.vscode/settings.json`](.vscode/settings.json)

## 必要な拡張機能

| 用途 | 拡張機能 ID | 必要性 |
| --- | --- | --- |
| C# の semantic token を提供し、この設定の `editor.semanticTokenColorCustomizations` を反映する | `ms-dotnettools.csharp` | 必須 |
| C# 拡張機能の .NET 実行環境を提供する | `ms-dotnettools.vscode-dotnet-runtime` | 必須（通常は C# 拡張機能の依存として導入） |
| このワークスペースの既定フォーマッタ | `ms-dotnettools.csdevkit` | 配色には不要。フォーマット設定を使う場合のみ必要 |

インストール例:

```powershell
code --install-extension ms-dotnettools.csharp
```

## 抽出した設定

```json
"editor.semanticTokenColorCustomizations": {
  "enabled": true,
  "rules": {
    "variable": { "foreground": "#9CDCFE" },
    "variable.readonly": {
      "foreground": "#b06e8e",
      "fontStyle": "bold"
    },
    "property": { "foreground": "#9b9eef" },
    "enum": {
      "foreground": "#ff4213",
      "fontStyle": "bold"
    },
    "enumMember": {
      "foreground": "#ff6a38",
      "fontStyle": "bold"
    },
    "extensionMethod": { "foreground": "#ff0099" },
    "keyword": { "foreground": "#1943ff" },
    "operator": { "foreground": "#ff1af7" },
    "modifier": { "foreground": "#cc54fc" },
    "parameter": { "foreground": "#6d96ef" },
    "class": {
      "foreground": "#11da0a",
      "fontStyle": "bold"
    },
    "class.static": {
      "foreground": "#00ff00",
      "fontStyle": "bold"
    },
    "field": {
      "foreground": "#d8f641",
      "fontStyle": "bold"
    },
    "field.static": {
      "foreground": "#ff0000",
      "fontStyle": "bold"
    },
    "constant": {
      "foreground": "#d14848",
      "fontStyle": "bold"
    },
    "method.constructor": {
      "foreground": "#ff8800",
      "fontStyle": "italic"
    },
    "method.deprecated": {
      "fontStyle": "strikethrough"
    }
  }
},
"editor.semanticHighlighting.enabled": true,
"editor.tokenColorCustomizations": {
  "textMateRules": [
    {
      "scope": [
        "keyword.operator.expression.pattern.is.cs",
        "keyword.operator.comparison.cs",
        "keyword.operator.logical.cs",
        "keyword.operator.assignment.cs"
      ],
      "settings": {
        "foreground": "#ffcc00",
        "fontStyle": "italic bold"
      }
    },
    {
      "scope": ["entity.name.variable.local.cs"],
      "settings": { "foreground": "#e1852e" }
    },
    {
      "scope": ["entity.name.variable"],
      "settings": { "foreground": "#46acff" }
    },
    {
      "scope": "variable.other.readwrite.cs",
      "settings": { "foreground": "#964c01" }
    },
    {
      "scope": [
        "variable.other.object.cs",
        "entity.name.variable.property.cs"
      ],
      "settings": { "foreground": "#ff0000" }
    },
    {
      "scope": "variable.other.property",
      "settings": { "foreground": "#535589" }
    },
    {
      "scope": "variable.other.readonly.cs",
      "settings": {
        "foreground": "#ff2727",
        "fontStyle": "bold"
      }
    },
    {
      "scope": [
        "constant",
        "support.constant",
        "string",
        "numeric",
        "constant.numeric",
        "constant.language",
        "variable.other.constant",
        "variable.other.constant.static",
        "entity.name.variable.tuple-element.cs",
        "keyword.operator.expression.pattern.is.cs"
      ],
      "settings": { "foreground": "#FF4500" }
    },
    {
      "scope": [
        "variable.other.enummember",
        "variable.other.enummember.cpp",
        "constant.language.enum",
        "constant.language.enumMember",
        "storage.type.enum"
      ],
      "settings": { "foreground": "#ff0000" }
    },
    {
      "scope": [
        "entity.name.function",
        "support.function"
      ],
      "settings": { "foreground": "#b665f7" }
    },
    {
      "scope": "storage.modifier.readonly.cs",
      "settings": {
        "foreground": "#ff1988",
        "fontStyle": "bold"
      }
    },
    {
      "scope": "keyword.modifier.cs",
      "settings": {
        "foreground": "#ff8800",
        "fontStyle": "bold"
      }
    },
    {
      "scope": "storage.type.string.cs",
      "settings": {
        "foreground": "#FF00FF",
        "fontStyle": "bold"
      }
    },
    {
      "scope": [
        "keyword.operator",
        "variable.parameter"
      ],
      "settings": { "foreground": "#ff7912" }
    },
    {
      "scope": "punctuation.separator.comma",
      "settings": {
        "foreground": "#4dff00",
        "fontStyle": "bold underline italic"
      }
    },
    {
      "scope": "punctuation.section.brackets",
      "settings": { "foreground": "#FF0000" }
    },
    {
      "scope": "support.class",
      "settings": {
        "foreground": "#ff2727",
        "fontStyle": "bold"
      }
    },
    {
      "scope": "entity.name.variable.field",
      "settings": {
        "foreground": "#ff2727",
        "fontStyle": "bold"
      }
    },
    {
      "scope": ["storage.type.accessor.init.cs"],
      "settings": {
        "foreground": "#ff0000",
        "fontStyle": "italic bold underline"
      }
    }
  ]
}
```
