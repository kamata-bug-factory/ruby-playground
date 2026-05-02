# Ruby に入門する

## はじめに

今回転職することになりました。
次の職場では Ruby 使いが多いそうなので、見習い Rubyist になっておきます。

このリポジトリを使ってお勉強しています 👇

https://github.com/kamata-bug-factory/ruby-playground

## 前提

私の環境は、以下の通りです。

- **OS:** macOS Sequoia 15.7.4 (Apple Silicon)
- **ツール:** Homebrew, mise
- **エディタ:** Visual Studio Code 1.118.1

## 1. 環境構築

### 1.1 Ruby をインストールする

mise を使って Ruby 3.3 をインストールします。

```bash
mise use ruby@3.3
```

私の環境でビルドに失敗し、インストールが中断されました。

```text
*** Following extensions are not compiled:
psych:
        Could not be configured. It will not be installed.
        Check /var/folders/pv/yx6lzfwd77x2kxvdb7bqdnmw0000gn/T/ruby-build.20260426011417.77099.ETg6TY/ruby-3.3.11/ext/psych/mkmf.log for more details.
BUILD FAILED (macOS 15.7.4 on arm64 using ruby-build 20260422)
```

ログファイル (`mkmf.log`) を確認したところ、`yaml.h` が見つからないことが原因だとわかりました。

```text
conftest.c:3:10: fatal error: 'yaml.h' file not found
    3 | #include <yaml.h>
      |          ^~~~~~~~
1 error generated.
```

Homebrew で `libyaml` をインストールしたのち、再度 Ruby をインストールします。

```bash
# 不足しているライブラリをインストール
brew install libyaml
# Ruby のインストールとローカルへの適用
mise use ruby@3.3
```

今度はビルドに成功し、プロジェクトルートに `mise.toml` が作成されました。

```toml:mise.toml
[tools]
ruby = "3.3"
```

正しいバージョンの Ruby がインストールされていることも確認できました。

```bash
ruby -v
# ruby 3.3.11 (2026-03-26 revision 1f2d15125a) [arm64-darwin24]
```

### 1.2 Ruby 向けの言語サーバーをインストールする

VS Code に **Ruby LSP** 拡張機能をインストールします。

> The Ruby LSP is an implementation of the language server protocol for Ruby, used to improve rich features in editors.

https://shopify.github.io/ruby-lsp/

[こちら](https://shopify.github.io/ruby-lsp/version-managers.html) を参考に、使っているバージョン管理ツールを指定します。

```json:.vscode/settings.json
{
  "rubyLsp.rubyVersionManager": {
    "identifier": "mise"
  }
}
```

## 2. Ruby の文法を学ぶ

これを読みました 👇

https://www.ruby-lang.org/ja/documentation/quickstart/

作成したソースコードは次のコマンドで実行できます。

```bash
ruby ri20min.rb
```

## 3. Ruby の Linter/Formatter を導入する

Ruby 開発において最も標準的に使われている Linter/Formatter は **RuboCop** のようです。

> RuboCop is a Ruby static code analyzer (a.k.a. linter) and code formatter.

https://github.com/rubocop/rubocop

### 3.1 RuboCop をインストールする

Ruby プロジェクトでは、依存関係を管理するために Bundler を使います。
Bundler を初期化し、`Gemfile` を生成します。

```bash
bundle init
```

生成された `Gemfile` の開発・テストグループに `rubocop` を追記します。
アプリの実行時に読み込む必要がないため、`require: false` を指定します。

```ruby:Gemfile
group :development, :test do
  gem 'rubocop', require: false
end
```

次のコマンドで依存関係をインストールします。

```bash
bundle install
```

RuboCop のルールは `.rubocop.yml` に記載します。

```yaml:.rubocop.yml
AllCops:
  NewCops: enable
  Exclude:
    - "vendor/**/*"
    - "bin/*"
```

### 3.2 VS Code で RuboCop を使う

VS Code の Ruby LSP 拡張機能では、Linter/Formatter として RuboCop を使用できます。
次のように設定を追加します。

```diff_json:.vscode/settings.json
{
  "rubyLsp.rubyVersionManager": {
    "identifier": "mise"
  },
+ "rubyLsp.formatter": "rubocop",
+ "[ruby]": {
+   "editor.defaultFormatter": "Shopify.ruby-lsp",
+   "editor.formatOnSave": true
+ }
}
```

### 3.3 動作確認

RuboCop が正常に導入されると、[20分ではじめるRuby](https://www.ruby-lang.org/ja/documentation/quickstart/) で作成した `ri20min.rb` に警告が出ます。
3.2 の設定により、Command+S で Formatter が効き、フォーマットに関する警告が消えるはずです。

ターミナルからは次のコマンドで実行できます。

```bash
# チェックの実行
bundle exec rubocop
# 自動修正の実行
bundle exec rubocop -A
```

## おわりに

次回は Rails に入門します。
