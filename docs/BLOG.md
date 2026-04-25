# Ruby に入門する

## はじめに

## 前提

私の環境は、以下の通りです。

- **OS:** macOS Sequoia 15.7.4
- **ツール:** Homebrew, mise

## 1. Ruby をインストールする

```bash
mise install ruby@3.3
```

私の環境では、ビルドに失敗しました。
`psych` がコンパイルできないと言われている 🤔

```
*** Following extensions are not compiled:
psych:
        Could not be configured. It will not be installed.
        Check /var/folders/pv/yx6lzfwd77x2kxvdb7bqdnmw0000gn/T/ruby-build.20260426011417.77099.ETg6TY/ruby-3.3.11/ext/psych/mkmf.log for more details.
BUILD FAILED (macOS 15.7.4 on arm64 using ruby-build 20260422)
```

`mkmf.log` を確認したところ、`yaml.h` が見つからないことが原因だったみたいです。

```
conftest.c:3:10: fatal error: 'yaml.h' file not found
    3 | #include <yaml.h>
      |          ^~~~~~~~
1 error generated.
```

Homebrew で `libyaml` をインストールします。

```bash
brew install libyaml
```

その後、もう一度 Ruby をインストールしたところ

```bash
mise use ruby@3.3
```

今度は通りました。

```
ruby@3.3.11     ==> Installed ruby-3.3.11 to /Users/kazukikamata/.local/share/mise/installs/ruby/3.3.11    ✔
mise ~/workspace/ruby-playground/mise.toml tools: ruby@3.3.11
```

プロジェクトルートに `mise.toml` が作成されます。

```toml:mise.toml
[tools]
ruby = "3.3"
```

正しいバージョンがインストールされていることも確認できました。

```bash
ruby-playground % ruby -v
ruby 3.3.11 (2026-03-26 revision 1f2d15125a) [arm64-darwin24]
```