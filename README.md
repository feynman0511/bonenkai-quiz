# 忘年会クイズアプリ

仕様は `bonenkai-quiz-spec.md` を参照。

## ファイル

| ファイル | 内容 |
|---|---|
| `index.html` | 参加者用ページ（QRコードで開く） |
| `admin.html` | 幹事用管理画面（Googleログイン） |
| `firebase-config.js` | Firebase接続設定（要編集） |
| `firestore.rules.example` | セキュリティルールの雛形（`firestore.rules` にコピーして幹事メールを記入。`firestore.rules` はgit管理外） |
| `style.css` | 共通スタイル |

## セットアップ

1. [Firebaseコンソール](https://console.firebase.google.com/)でプロジェクトを作成
2. **Authentication** で「匿名」と「Google」を有効化。Authentication → 設定 → 承認済みドメインに `<ユーザー名>.github.io` を追加
3. **Firestore Database** を作成（本番モード）
4. プロジェクトの設定 → マイアプリ → Webアプリを追加し、表示された設定値を `firebase-config.js` に貼り付け
5. `firestore.rules` の `YOUR_ADMIN_EMAIL@gmail.com` を幹事のGoogleアカウントに書き換え、Firestore → ルール に貼り付けて公開
6. GitHubのpublicリポジトリにpushし、Settings → Pages で公開
7. `https://<ユーザー名>.github.io/<リポジトリ>/admin.html` にログインし、問題JSONを登録
8. `https://<ユーザー名>.github.io/<リポジトリ>/` からQRコードを作成

ローカル確認: `python -m http.server 8000` → http://localhost:8000/ （`localhost` も承認済みドメインに含まれます）

## 当日の流れ

1. 全員揃ったら管理画面で「受付開始」
2. 参加者が回答。管理画面で「○ / ○人」と未回答者を確認
3. 「締切」→ 後半に「順位を表示する」で発表
4. 終了後「参加者・回答を全削除」、または Pages の公開を止める

## セキュリティ設計

- 正解は `secret/answers`（幹事のみ読み書き）に保存。リポジトリには含まれない
- 問題文・選択肢も受付中しか参加者に読ませない（事前閲覧防止）
- 回答は `responses/{匿名UID}` に create のみ許可（上書き不可）、受付中のみ
- 回答時間（各問の所要ミリ秒）を保存済み。管理画面のチェックボックスで順位への反映を切替可能
- 注意: 匿名IDは端末（ブラウザ）単位。ブラウザデータ削除やシークレットモードで別IDになる点は仕様上の限界
