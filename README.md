# RAG構築ハンズオンの事前準備

ハンズオンを開始する前に、以下のツールをインストールしておいて下さい。

## Bunのインストール

TypeScriptの実行環境にはBunを使います。

https://bun.sh/docs/installation

### Windows

```powershell
powershell -c "irm bun.sh/install.ps1|iex"
```

### macOS

```bash
brew install bun
```

### インストール確認
```bash
bun --version
# バージョンが表示される事を確認してください
1.2.15
```

### 動作確認
```bash
bun run index.ts

# Bunがインストールされていれば、index.tsの実行結果が表示されます
Hello via Bun!
```

## Ollamaのインストール

[Ollama](https://ollama.com/)はローカルでLLMを実行するためのツールです。

### Windows

1. [Ollama公式サイト](https://ollama.ai/download)からWindows版をダウンロード
2. `OllamaSetup.exe`を実行してインストール
3. インストール後、自動的にOllamaサービスが開始されます

### macOS

**方法1: 公式インストーラー（推奨）**
1. [Ollama公式サイト](https://ollama.ai/download)からmacOS版をダウンロード
2. `Ollama.dmg`をマウントして`Ollama.app`をApplicationsフォルダにドラッグ
3. Applicationsフォルダから`Ollama.app`を起動

**方法2: Homebrew**
Homebrewで管理したい場合はbrewでインストールして下さい

```bash
brew install ollama
```

### インストール確認
```bash
ollama --version

# バージョンが表示される事を確認してください
ollama version is 0.9.0
```

## ハンズオンで使用するモデルのダウンロード

ハンズオンで使用するモデルを事前にダウンロードします。
ハンズオンでは次の2種類のモデルを使用します。

1. 大規模言語モデル（LLM）
2. 埋め込みモデル（Embedding Model）

1は質問に対して回答を生成するためのモデル
2はテキストをベクトル（数値の配列）に変換し、文書の類似性検索を可能にするモデル

Ollamaには豊富なモデルライブラリがあります。GitHubのRepositoryのように、多くのLLMがそこで公開されており、簡単にダウンロードして使用できます。

今回はそのライブラリからモデルをダウンロードします。

### 大規模言語モデル（LLM）

Googleが開発したgemma2というモデルを日本語向けに調整したものを使います。

https://ollama.com/schroneko/gemma-2-2b-jpn-it

```bash
# メインのLLMモデル（約2.8GB）
ollama pull schroneko/gemma-2-2b-jpn-it

# 実行結果
pulling manifest 
pulling 1b3b86a920e7: 100% ▕███████████████████████▏ 2.8 GB                         
pulling 4ea0df93422f: 100% ▕███████████████████████▏  445 B                         
pulling 2490e7468436: 100% ▕███████████████████████▏   65 B                         
pulling e2155a2ac827: 100% ▕███████████████████████▏  413 B                         
verifying sha256 digest 
writing manifest 
success 
```

### 埋め込みモデル（約274MB）
https://ollama.com/library/nomic-embed-text
```
ollama pull nomic-embed-text
```

### ダウンロード確認
モデルがダウンロード出来ているかを確認しましょう。

```bash
# インストール済みモデルの一覧表示
ollama list

# 実行結果にgemma-2-2b-jpn-itとnomic-embed-textが表示されていればダウンロードが出来てます
NAME                                  ID              SIZE      MODIFIED          
nomic-embed-text:latest               0a109f422b47    274 MB    7 seconds ago        
schroneko/gemma-2-2b-jpn-it:latest    fcfc848fe62a    2.8 GB    50 minutes ago 
```

##  Ollamaの起動確認

**Windows**
- Ollamaは通常、インストール後に自動的にサービスとして起動します
- タスクマネージャーで「Ollama」プロセスが実行中か確認

**Mac**
```bash
# Ollamaサービスを起動（必要に応じて）
ollama serve
```

### 動作確認

LLM
ダウンロードしたLLMをOllamaを通して使ってみましょう。

```bash
ollama run schroneko/gemma-2-2b-jpn-it "こんにちは?"

# 出力例
こんにちは！😊  何か用ですか？
```
毎回異なる回答が表示されないため、違っていても気にしないでください。LLMは質問のたびに新しい回答を生成するため、全く同じ内容になることはありません。

埋め込みモデル
「Hello World」のテキストがベクトル化されるかを試すためのコマンドです

```bash
curl -X POST http://localhost:11434/api/embeddings \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nomic-embed-text",
    "prompt": "Hello World"
  }'

# 出力例 長いので冒頭だけ
{
  "embedding": [
    0.21180304884910583,
    1.2743878364562988,
```

### TIPS
なぜAPIが利用できるのか

```bash
ollama serve
# → http://localhost:11434 でAPIサーバーが起動している
```

Ollamaは起動するとバックグラウンドでHTTPサーバーが自動的に起動していて、APIサーバとして使えるようになっています。

なんとAPIを使って、テキスト生成やモデルの管理なども出来るのです
https://github.com/ollama/ollama/blob/main/docs/api.md