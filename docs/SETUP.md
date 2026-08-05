# セットアップ状況

ig_akiyaと同じ構成でスキャフォールド(2026-08-05)。

- Facebookページ: Nemuneko (id `1259214413936131`)
- Instagramアカウント: `@nemu_neco99` (`INSTAGRAM_ACCOUNT_ID` = `17841457894433944`)
- GitHub Secrets (`INSTAGRAM_ACCESS_TOKEN`, `INSTAGRAM_ACCOUNT_ID`) は設定済み(2026-08-05)

## 1. アクセストークン取得 — 完了

Meta App `akiya.app` (APP_ID: `27566135536361698`) をig_akiyaと共用。長期トークン発行・GitHub Secrets設定済み。

**ハマったポイント（次回同様の追加をする際の参考）:**
- Graph API Explorerの「Generate Access Token」ボタンを素直に押すと、新しいページ/IGアカウントの組み合わせでは「Instagram Login」フローに誘導され、`IGAA`プレフィックスのトークンが発行されてしまう(`graph.facebook.com`では使えない)。
- 回避策: Graph API Explorerを介さず、直接OAuthダイアログURLを叩く。
  ```
  https://www.facebook.com/v22.0/dialog/oauth?client_id=27566135536361698&redirect_uri=https%3A%2F%2Fdevelopers.facebook.com%2Ftools%2Fexplorer%2Fcallback&response_type=token&scope=instagram_basic,instagram_content_publish,pages_show_list,pages_read_engagement
  ```
  「設定を編集」からページ・IGアカウントを明示的に選択すると、`EAA`/`EAG`プレフィックスの正しいトークンが発行される。
  - `pages_manage_metadata`をscopeに含めると"Invalid Scopes"エラーになるので外すこと。
- 新しく許可したページは`me/accounts`一覧にすぐ反映されないことがある。その場合は該当ページIDを直接指定して確認する: `GET /{page-id}?fields=name,instagram_business_account`

## 2. GitHub Secretsの設定 — 完了

## 3. コンテンツ追加

`content/YYYY-MM-DD/` 配下に以下を置くと、その日の `post.yml` 実行で投稿される:
- `video.mp4`
- `caption.txt`
- `thumb_offset.txt`（任意）

## 4. トークンの更新（手動運用）

長期トークンは発行から約60日で失効。自動更新ワークフローは組み込んでいない(ig_akiyaと同じ方針)ので、期限前に手動で再実行:

```
python scripts/exchange_token.py <新しい短期トークン>
gh secret set INSTAGRAM_ACCESS_TOKEN --repo shiro0507/ig_sleep --body "<新しい長期トークン>"
```
