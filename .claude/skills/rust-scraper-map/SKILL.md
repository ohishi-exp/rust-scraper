---
name: rust-scraper-map
generated-from: rust-scraper:43bfaf03b98b0d076864e4ed6a9fc80363d94ec7
paths: [src/, src/etc/, examples/]
description: rust-scraper (scraper-service) の構造ナビゲーション。ETC 明細 CSV ダウンロード / etc-meisai.jp スクレイパー / chromiumoxide CDP ダイアログ処理 / CSVダウンロード / tower::Service / ScraperConfig / ScraperError / scraper-service / 実機テスト手順の詳細を収録。
---

# rust-scraper-map — 構造ナビゲーション

## CLAUDE.md から移設 (2026-07-07)

## プロジェクト概要

ETC利用照会サービス（etc-meisai.jp）から利用明細CSVを自動ダウンロードするスクレイパー。
Router（router-service）からInProcessで呼び出される。

**参照元Go版:** https://github.com/yhonda-ohishi-pub-dev/scrape-vm

## 現在の状態

### 完了
- [x] Phase S1: 基盤構築（Cargo.toml, traits.rs, config.rs, error.rs）
- [x] Phase S2: ETC Scraper実装（chromiumoxide使用）
- [x] Phase S3: tower::Service実装
- [x] ビルド・テスト通過
- [x] GitHubプッシュ済み: https://github.com/yhonda-ohishi-pub-dev/rust-scraper
- [x] ログイン処理: JavaScriptで直接値設定する方式に修正済み
- [x] 検索処理: 検索条件指定→検索ボタンクリックまで動作確認済み
- [x] CSVリンククリック: 動作確認済み
- [x] confirmダイアログ処理: EventJavascriptDialogOpeningで自動OK応答
- [x] CSVダウンロード・リネーム: 動作確認済み（2025-12-26）

### 実装済み機能
- JavaScriptダイアログハンドラ（`src/etc/scraper.rs:199-219`）
  - `EventJavascriptDialogOpening`イベントをリッスン
  - `HandleJavaScriptDialogParams`で`accept: true`を応答
- Windowsネイティブパス対応（`\\?\`プレフィックス除去）
- ダウンロードパス設定（CDP `SetDownloadBehaviorParams`使用）

## 技術選定

### chromiumoxide を選択した理由
- `headless_chrome`は`ring`クレートに依存し、Windows環境でgccビルドエラーが発生
- `chromiumoxide`は`native-tls`フィーチャで`ring`依存を回避可能
- async/awaitネイティブ対応

### Cargo.toml 重要設定
```toml
chromiumoxide = { version = "0.8", default-features = false, features = ["tokio-runtime", "_fetcher-native-tokio"] }
```

## ディレクトリ構成

```
c:\rust\rust-scraper\
├── Cargo.toml
├── README.md
├── examples/
│   └── scrape_test.rs    # 実機テスト用（環境変数で認証情報を指定）
└── src/
    ├── lib.rs            # ライブラリエントリーポイント
    ├── traits.rs         # Scraper trait定義
    ├── config.rs         # ScraperConfig
    ├── error.rs          # ScraperError
    ├── service.rs        # tower::Service実装
    └── etc/
        ├── mod.rs
        └── scraper.rs    # ETC Scraper実装
```

## コマンド

```bash
# ビルド
cargo build

# テスト
cargo test

# 実機テスト（Chrome表示モード）
ETC_USERNAME=ohishiexp ETC_PASSWORD=ohishi11 cargo run --example scrape_test
```

## 関連プロジェクト

- **router-service**: `C:\rust\rust-router\router-service\`
  - このscraper-serviceを呼び出す側
  - Cargo.tomlで `scraper-service = { git = "..." }` として参照予定
