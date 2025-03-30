# Azure AIサービスを利用したRAG 
 
 
## 目次
 
1. [プロジェクト概要](#プロジェクト概要)
2. [前提条件](#前提条件)
3. [環境構築](#環境構築)
4. [使用方法](#使用方法)
 
 
 
## プロジェクト概要
 
RAG（Retrieval-Augmented Generation）は、事前に収集した大量のデータから関連する情報を抽出し、その情報を基にユーザーに適切な応答を生成する技術です。
このプロジェクトでは、AzureのAIサービスを活用し、アップロードされた画像やドキュメントからテキストを抽出して、それを検索ベースでリクエスト内容に関連する情報を返答する仕組みを構築しています。
複雑なレイアウトのPDFファイル等も、セマンティックチャンキング法により高精度に抽出することが可能です。
主な利用シナリオとしては、システム開発における要件定義書や設計書といった資料を取り扱い、これらから必要な情報を素早く引き出せる点が挙げられます。
 <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 600">
  <!-- 背景 -->
  <rect width="800" height="600" fill="#f0f5ff" />
  
  <!-- タイトル -->
  <text x="400" y="40" font-family="Arial" font-size="24" font-weight="bold" text-anchor="middle" fill="#0078D4">Azure AIサービスを利用したRAGアーキテクチャ</text>
  
  <!-- クライアントセクション -->
  <rect x="50" y="80" width="200" height="180" rx="10" fill="#E1EFFF" stroke="#0078D4" stroke-width="2" />
  <text x="150" y="100" font-family="Arial" font-size="16" font-weight="bold" text-anchor="middle" fill="#0078D4">フロントエンド</text>
  <text x="150" y="120" font-family="Arial" font-size="14" text-anchor="middle" fill="#333">(React)</text>
  
  <!-- クライアントコンポーネント -->
  <rect x="70" y="140" width="160" height="30" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="150" y="160" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">ドキュメントアップロード</text>
  
  <rect x="70" y="180" width="160" height="30" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="150" y="200" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">チャットインターフェース</text>
  
  <rect x="70" y="220" width="160" height="30" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="150" y="240" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">検索結果表示</text>
  
  <!-- バックエンドセクション -->
  <rect x="300" y="80" width="450" height="420" rx="10" fill="#E1EFFF" stroke="#0078D4" stroke-width="2" />
  <text x="525" y="100" font-family="Arial" font-size="16" font-weight="bold" text-anchor="middle" fill="#0078D4">バックエンド (Azure Functions)</text>
  
  <!-- Azure AI サービス -->
  <rect x="330" y="130" width="180" height="140" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="150" font-family="Arial" font-size="14" font-weight="bold" text-anchor="middle" fill="#0078D4">ドキュメント処理</text>
  
  <rect x="340" y="170" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="190" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">Azure Document Intelligence</text>
  
  <rect x="340" y="210" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="230" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">セマンティックチャンキング</text>
  
  <!-- Azure AI Search -->
  <rect x="330" y="280" width="180" height="90" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="300" font-family="Arial" font-size="14" font-weight="bold" text-anchor="middle" fill="#0078D4">検索サービス</text>
  
  <rect x="340" y="320" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="340" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">Azure AI Search</text>
  
  <!-- Azure OpenAI -->
  <rect x="330" y="380" width="180" height="90" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="400" font-family="Arial" font-size="14" font-weight="bold" text-anchor="middle" fill="#0078D4">回答生成</text>
  
  <rect x="340" y="420" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="420" y="440" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">Azure OpenAI</text>
  
  <!-- SharePoint連携 -->
  <rect x="540" y="130" width="180" height="90" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="630" y="150" font-family="Arial" font-size="14" font-weight="bold" text-anchor="middle" fill="#0078D4">外部連携</text>
  
  <rect x="550" y="170" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="630" y="190" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">SharePoint連携</text>
  
  <!-- インデックス管理 -->
  <rect x="540" y="230" width="180" height="140" rx="5" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="630" y="250" font-family="Arial" font-size="14" font-weight="bold" text-anchor="middle" fill="#0078D4">インデックス管理</text>
  
  <rect x="550" y="270" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="630" y="290" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">インデックス作成</text>
  
  <rect x="550" y="310" width="160" height="30" rx="5" fill="#CCE4FF" stroke="#0078D4" stroke-width="1" />
  <text x="630" y="330" font-family="Arial" font-size="12" text-anchor="middle" fill="#333">スキルセット設定</text>
  
  <!-- ドキュメントフロー -->
  <path d="M250 170 L330 190" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  <path d="M250 200 L310 200 L310 340 L330 340" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  <path d="M250 230 L300 230 L300 430 L330 430" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  
  <path d="M420 240 L420 280" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  <path d="M420 370 L420 380" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  
  <path d="M510 190 L540 190" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  <path d="M510 340 L540 290" stroke="#0078D4" stroke-width="2" fill="none" marker-end="url(#arrowhead)" />
  
  <!-- 矢印定義 -->
  <defs>
    <marker id="arrowhead" markerWidth="10" markerHeight="7" refX="9" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#0078D4" />
    </marker>
  </defs>
  
  <!-- データフロー説明 -->
  <rect x="50" y="520" width="700" height="60" rx="10" fill="#ffffff" stroke="#0078D4" stroke-width="1" />
  <text x="400" y="545" font-family="Arial" font-size="14" text-anchor="middle" fill="#333">
    ドキュメントからのテキスト抽出 → セマンティック検索 → 関連情報の取得 → 回答生成
  </text>
  <text x="400" y="565" font-family="Arial" font-size="12" text-anchor="middle" fill="#666">
    複雑なレイアウトのPDFも高精度に処理し、要件定義書や設計書から必要な情報を迅速に引き出します
  </text>
</svg>
 
 
## 前提条件
 
このプロジェクトを動かすために必要なツールやリソース
 
- **Azure サブスクリプション**
- **Azure Functions Core Tools**
- **Azure Document Intelligence**
- **Azure AI Search**
- **Azure OpenAI**
- **Python 3.10**
- **VSCode**
- **Azure CLI**
 
 
 
## 環境構築
 
```
git clone git@gitlab.com:intelligentforce/azure_rag.git
cd azure_rag
```
 
### 仮想環境の構築と必要なライブラリのインストール
 
```
pyenv local 3.10.15
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
 
### local_settings.jsonで環境変数の設定
それぞれのバリューには適切なものを入力してください
```
{
    "IsEncrypted": false,
    "Values": {
      "AzureWebJobsStorage": "seDevelopmentStorage=true",
      "FUNCTIONS_WORKER_RUNTIME": "python",
      "AZURE_OPENAI_API_KEY":"Your AZURE_OPENAI_API_KEY",
      "AZURE_OPENAI_ENDPOINT":"Your AZURE_OPENAI_ENDPOINT",
      "AZURE_OPENAI_EMBEDDING_API_KEY":"Your AZURE_OPENAI_EMBEDDING_API_KEY",
      "AZURE_OPENAI_EMBEDDING_ENDPOINT":"Your AZURE_OPENAI_EMBEDDING_ENDPOINT",
      "DOCUMENT_INTELLIGENCE_API_KEY":"Your DOCUMENT_INTELLIGENCE_API_KEY",
      "DOCUMENT_INTELLIGENCE_ENDPOINT":"Your DOCUMENT_INTELLIGENCE_ENDPOINT",
      "AZURE_SEARCH_ENDPOINT":"Your AZURE_SEARCH_ENDPOINT",
      "AZURE_SEARCH_ADMIN_KEY":"Your AZURE_SEARCH_ADMIN_KEY"
    },
    "Host": {
      "CORS": "*"
    }
}
```
 
 
 
## 使用方法
 
```
cd rag_api
func start
```
 
上記のコマンドの実行でバックエンドサーバーを立ち上げます。<br>

```
cd ..
cd rag_cliant
npm start
``` 
上記のコマンドの実行でフロントエンドサーバーを立ち上げます。<br>


UIを操作しフィルのアップロードを行う．
"メッセージを入力"の欄から質問を入力すると，アップロードしたファイルの内容を検索して適切な回答が返ってくる．
