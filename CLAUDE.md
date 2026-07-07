# CLAUDE.md - scraper-service

ETC利用照会サービス（etc-meisai.jp）から利用明細CSVを自動ダウンロードするスクレイパー。Router（router-service）からInProcessで呼び出される。

## ビルド / テスト

```bash
cargo build
cargo test

# 実機テスト（Chrome表示モード）
ETC_USERNAME=ohishiexp ETC_PASSWORD=ohishi11 cargo run --example scrape_test
```

## 技術制約 (必ず守ること)

- **chromiumoxide を native-tls で使う** — `headless_chrome` は `ring` クレート依存で Windows gcc ビルドエラーが発生するため `chromiumoxide` に統一。`ring` 依存を混入させない。
- **Cargo.toml の chromiumoxide 設定は変えない**:
  ```toml
  chromiumoxide = { version = "0.8", default-features = false, features = ["tokio-runtime", "_fetcher-native-tokio"] }
  ```
  `default-features = false` + `_fetcher-native-tokio` を外すと openssl/ring 依存が復活する。

## 参照元 Go 版

https://github.com/yhonda-ohishi-pub-dev/scrape-vm

詳細 (アーキテクチャ・実装状態・ディレクトリ構成・技術選定経緯・関連プロジェクト) は rust-scraper-map skill を参照。
