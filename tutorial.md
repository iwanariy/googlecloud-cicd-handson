# Cloud Run で実践する Google Cloud での CI / CD ハンズオン

<walkthrough-watcher-constant key="region" value="asia-northeast1"></walkthrough-watcher-constant>
<walkthrough-watcher-constant key="zone" value="asia-northeast1-a"></walkthrough-watcher-constant>
<walkthrough-watcher-constant key="sa" value="sa-baremetal"></walkthrough-watcher-constant>

## 始めましょう

Cloud Shell をベースにローカル開発、Google Cloud での CI / CD を体験いただくハンズオンです。以下の流れで実際のアプリケーション開発を体験いただきます。

1. ローカルでの開発
1. Cloud Run をベースにした CI / CD
1. 高度なデプロイオプションの利用

<walkthrough-tutorial-duration duration="45"/> 
**所要時間**: 約 45 分

**前提条件**:

- Google Cloud 上にプロジェクトが作成してある

**[開始]** ボタンをクリックして次のステップに進みます。

## プロジェクトの設定

この手順の中で実際にリソースを構築する対象のプロジェクトを選択してください。

<walkthrough-project-billing-setup permissions="compute.googleapis.com"></walkthrough-project-billing-setup>

## 1. ローカルでの開発

[Cloud Shell エディタ](https://cloud.google.com/shell/docs/launching-cloud-shell-editor?hl=ja) は個人ごとに割り当てられる開発環境としてご利用いただけます。このセクションでは、以下のような流れで開発者の個人環境での開発を実施します。

1. サンプルコードのダウンロード
1. アプリの起動まで
1. コードの変更、リアルタイムでの更新
1. デバッグ
1. 個人開発環境へのデプロイ
1. ログの確認

## 1.1. サンプルコードのダウンロード

まず以下のリンクから、Cloud Shell エディタを起動してみましょう。<walkthrough-editor-open-file filePath="cloudshell_open/googlecloud-cicd-handson/README.md">Cloud Shell エディタで README を開く</walkthrough-editor-open-file>

1.  ステータス バーから
    <walkthrough-editor-spotlight spotlightId="cloud-code-status-bar">Cloud Code</walkthrough-editor-spotlight> を開き、
1.  <walkthrough-editor-spotlight spotlightId="cloud-code-new-app">New 
    Application</walkthrough-editor-spotlight> > <walkthrough-editor-spotlight spotlightId="cloud-code-new-app-cloud-run">Cloud Run application</walkthrough-editor-spotlight> を選びます。
1.  Cloud Run のサンプル一覧から、**Go: Cloud Run** を選択、
1.  ダウンロード先として任意の場所を選び **Create New Application** をクリックします。
1.  Cloud Shell エディタがリロードされたら、
    <walkthrough-editor-spotlight spotlightId="file-explorer">explorer view</walkthrough-editor-spotlight> でファイルの一覧を確認しましょう。

## 1.2. アプリの起動まで

ではこのアプリケーションを Cloud Shell のローカルで、Cloud Run エミュレータを使い起動してみます。

1.  アプリケーションをビルドし、エミュレータで実行するには
    <walkthrough-editor-spotlight spotlightId="cloud-code-run-on-cloud-run-emulator">Run
    on Cloud Run Emulator</walkthrough-editor-spotlight> を選択します。
1.  **Run/Debug on Cloud Run Emulator** タブが開いたら **Build Settings** の **Bulder** を **Buildpacks** に変更し、**Run** をクリックします。初回の起動には 5 分ほどかかります。
1.  サービスがデプロイされると、
    <walkthrough-editor-spotlight spotlightId="output">Output</walkthrough-editor-spotlight>
    パネルに以下のように表示されます。

    ```terminal
    http://localhost:8080
    Update successful
    ```
1.  Web preview ボタン <walkthrough-web-preview-icon/> を押し、"Preview on port 8080" を選んでみましょう。

おめでとうございます！アプリの起動はうまくいきましたね。

## 1.3. コードの変更、リアルタイム更新

コードの書き換えによって、アプリケーションがリアルタイムに更新されることを確認します。

1.  <walkthrough-editor-open-file filePath="index.html">index.html
    </walkthrough-editor-open-file> を開き 
    <walkthrough-editor-select-line filePath="index.html" startLine="32" endLine="68" startCharacterOffset="0" endCharacterOffset="60">
    callout 以下の p 要素</walkthrough-editor-select-line> を削除してみます。
1.  <walkthrough-editor-spotlight spotlightId="output">Output</walkthrough-editor-spotlight>
    パネルに

    ```terminal
    Update initiated
    Deploy started
    ```

    から始まり、最終的にはやはり以下のようなメッセージが表示されます。

    ```terminal
    http://localhost:8080
    Update successful
    ```
1.  Web プレビュー画面をリロードしてみましょう。

画面は更新されましたか？

## 1.4. デバッグ

ローカルでデバッグしてみましょう。

1.  アプリケーションをデバッグ モードで実行するには
    <walkthrough-editor-spotlight spotlightId="cloud-code-debug-on-cloud-run-emulator">Debug
    on Cloud Run Emulator</walkthrough-editor-spotlight> を選択します。
1.  **デバッグ パネル** が開き、デバッガが実際にアタッチされると、ステータス バーの色が変わります。
1.  **THREADS** を見てください。複数のアプリを並行で起動していくと接続ポートが増えていきますので、
    8080 番ポートでのみ開発をするには **デバッグ ツールバー** からいったんすべて停止してもいいかもしれません。その上で必要に応じ、参照する設定が **Cloud Run: Run/Debug Locally** であることを確認してデバッグを再開してください。
1.  <walkthrough-editor-open-file filePath="main.go">main.go
    </walkthrough-editor-open-file> を開き 
    <walkthrough-editor-select-line filePath="main.go" startLine="62" endLine="62" startCharacterOffset="0" endCharacterOffset="60">
    63 行目</walkthrough-editor-select-line> にブレイク ポイントを設定します。
1.  Web プレビュー <walkthrough-web-preview-icon/>、またはターミナルから `curl` コマンドなどでサービスにアクセスします。

ブレイク ポイントで停止しましたか？

## 1.5. 個人開発環境へのデプロイ

では個人の Cloud Run へ、Cloud Code プラグインを通してデプロイしてみましょう。

1.  ステータスバーから
    <walkthrough-editor-spotlight spotlightId="cloud-code-status-bar">Cloud
    Code</walkthrough-editor-spotlight> を選択し
1.  <walkthrough-editor-spotlight spotlightId="cloud-code-cloud-run-deploy">Deploy
    to Cloud Run</walkthrough-editor-spotlight> を選びます。
1.  もし Google Cloud への API 実行を確認するポップアップが表示されたら承認してください。
1.  もし Cloud Run API の有効化を求められたら、**Enable APIs** をクリックします。
1.  **Deploy to Cloud Run** タブが開いたら
1.  東京リージョン (asia-northeast1) を選択しましょう。
1.  ここでは誰からでも接続可能となる **Allow unauthenticated invocations** を選択します。
1.  **Build Settings** の **Bulder** を **Buildpacks** に変更します。
1.  **Deploy** をクリックします。
1.  しばらくすると **Deployment completed successfully! URL** が表示されます。
    実際にアクセスしてみましょう。

Cloud Run としてホストされたサービスにアクセスできましたか？

## 1.6. ログの確認

エミュレータやクラウド上の Cloud Run で出力されるログを確認してみます。

1.  ローカルでは <walkthrough-editor-spotlight spotlightId="output">Output
    </walkthrough-editor-spotlight> の右上で、どこからの出力を表示するかを選択できます。
    **Cloud Run: Run/Debug Locally** ではなく **Cloud Run: Run/Debug Locally - Detailed** を選ぶことでエミュレータ内部で出力されたログが確認できます。
1.  クラウド上の Cloud Run については、左側のメニュー
    <walkthrough-editor-spotlight spotlightId="cloud-code-run-icon">Cloud Run
    Explorer</walkthrough-editor-spotlight> で様々な情報が確認できます。
1.  さきほどデプロイしたサービスの上で右クリック、**View Logs** を選びます。
1.  新しいログを確認するためには
    <walkthrough-editor-spotlight spotlightId="cloud-code-logs-viewer-refresh">Logs
    refresh button</walkthrough-editor-spotlight> が利用できます。

ログは確認できましたか？

<walkthrough-footnote>ここまでで、開発者それぞれに与えられた環境での開発フローを見てきました。ここからは、チームとして製品を開発、CI / CD を回す方法を確認していきましょう。</walkthrough-footnote>

## これで終わりです

<walkthrough-conclusion-trophy></walkthrough-conclusion-trophy>

すべて完了しました。
