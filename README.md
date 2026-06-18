# hsun-skills

hsun 的個人 [Claude Code](https://code.claude.com) plugin marketplace。

## 安裝

在任何裝有 Claude Code 的機器上:

```
/plugin marketplace add <你的GitHub帳號>/hsun-skills
/plugin install commit-flow@hsun-skills
```

安裝後重新載入,skill 會以 namespace 形式出現:`/commit-flow:commit-flow`。

## 內含 plugins

| Plugin | 說明 |
|--------|------|
| `commit-flow` | 以 Conventional Commits 規範產生 commit 訊息並提交,同時嚴格遵守 Git Flow 分支保護與 release 守門規則。 |

## 目錄結構

```
hsun-skills/
├── .claude-plugin/
│   └── marketplace.json          # marketplace 目錄清單
└── plugins/
    └── commit-flow/
        ├── .claude-plugin/
        │   └── plugin.json        # plugin manifest
        └── skills/
            └── commit-flow/
                └── SKILL.md       # skill 內容
```

## 新增更多 skill

1. 在 `plugins/` 下新增 `<plugin-name>/` 資料夾(含 `.claude-plugin/plugin.json` 與 `skills/<skill>/SKILL.md`)。
2. 在 `.claude-plugin/marketplace.json` 的 `plugins` 陣列追加一筆。
3. commit 後 push,使用者重新執行 `/plugin marketplace update hsun-skills` 即可取得更新。
