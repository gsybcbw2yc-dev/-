# 文化祭 会計・在庫管理 Webアプリ（Firebase共有版）

## 先に知っておいてください
このZIPにはクラウド同期対応のアプリ本体、Firestoreルール、Firebase設定テンプレートが含まれます。
**公開URLはFirebaseプロジェクトを作成し、設定値を入れてデプロイするまで発行できません。** ChatGPTからあなたのGitHub/Firebaseアカウントにログインして公開することはできません。以下の手順で公開できます。

## ファイル
- `index.html` : アプリ本体
- `firebase-config.js` : Firebase Webアプリ設定（必須）
- `firestore.rules` : Firestoreアクセス制御ルール
- `README.md` : セットアップ手順

## 1. Firebaseプロジェクトを作成
1. https://console.firebase.google.com/ を開き、Googleアカウントでログイン。
2. 「プロジェクトを作成」。プロジェクト名例 `bunkasai-cashier`。
3. プロジェクト概要の歯車 → プロジェクトの設定 → 全般 → マイアプリ → Webアプリ（</>）を追加。
4. 表示されたFirebase SDK設定の `apiKey`, `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, `appId` を `firebase-config.js` に転記。
   - apiKeyはFirebaseクライアント識別子であり、サービスアカウント秘密鍵とは異なります。
   - サービスアカウント秘密鍵をブラウザやGitHub Pagesに置かないでください。

## 2. Authenticationを設定
1. Firebase Console → Authentication → 始める。
2. Sign-in methodで「メール/パスワード」を有効化。
3. Authentication → Users → Add userで管理者アカウントを作成。
4. そのユーザーのUIDをコピー。

## 3. Firestoreを設定
1. Firebase Console → Firestore Database → データベースを作成。
2. `firestore.rules` の内容をFirestoreの「ルール」に貼り付けて公開。
3. Firestoreの「データ」タブで `users` コレクションを作成し、管理者UIDと完全一致するドキュメントIDを作成。
4. ドキュメントに `role`（文字列）=`admin` を追加。
5. アプリへ管理者としてログインし、「管理者」→「初期商品を登録」を実行。
6. スタッフアカウントをAuthenticationで作成し、同様に `users/{スタッフUID}` ドキュメントを作り `role`（文字列）=`staff` に設定。

## 4. GitHub Pagesで公開
1. GitHubで新しいPublicまたはPrivateリポジトリを作成（GitHub Pagesのプラン/リポジトリ条件を確認）。
2. `index.html`, `firebase-config.js`, `firestore.rules`, `README.md` をリポジトリのルートにアップロード。
3. GitHubリポジトリ → Settings → Pages → Deploy from a branch → `main` / `/ (root)` → Save。
4. 公開URLは `https://<GitHubユーザー名>.github.io/<リポジトリ名>/` 形式。
5. Firebase Authentication → Settings → Authorized domains に `github.io` と、表示される実際のホスト名を追加（必要な場合）。
6. URLをiPad・スマホで開き、スタッフアカウントでログインしてテスト。

## 5. 運用前の重要チェック
- まずテスト商品・少額のテスト会計で、別々の2台から同時会計して在庫が正しいか確認。
- 取消で売上が除外され在庫が戻るか確認。
- 文化祭終了後に売上CSV・会計履歴CSV・JSONバックアップを保存。
- Firestoreの利用量・料金条件をFirebase Consoleで確認。
- Firestoreルールは最小限の初期構成です。実運用前に、管理者だけが取消可能など、必要な運用ポリシーに合わせて追加検証してください。

## 機能
- Firebase Authenticationによるメール・パスワードログイン
- Firestoreによる商品・売上・設定のリアルタイム同期
- 会計確定時のFirestoreトランザクション
- 商品別・日別の売上、原価、利益集計
- 在庫警告、会計履歴、取消処理
- CSV出力、JSONバックアップ
- 管理者・スタッフのロール分離
- iPad / スマートフォン対応

## 既知の制約
- この環境からGitHub/Firebaseにログインして公開操作を代行できないため、完成した公開URLはまだありません。
- 現在の取消処理は、売上ドキュメントを取消済みにし在庫を戻します。管理者の取消のみ許可したい場合はFirestoreルールとUIを合わせて変更してください。
- `firestore.rules` は各会計の明細と商品価格の完全なサーバー側照合までは行いません。会計の不正耐性を高める本番用途ではCloud Functions等のサーバー側会計処理を推奨します。
