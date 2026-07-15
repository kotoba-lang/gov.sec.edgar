# gov.sec.edgar

`data.sec.gov` の SEC EDGAR XBRL companyfacts API から取得した、米国 SEC 提出企業の生財務ファクト JSON snapshot を保全する DataLad dataset です。統合・要約用 fact（企業ごとの revenue/assets/net-income 等の要約 datom）は `gftdcojp/cloud-murakumo-market-intel` が生成します。

各 raw JSON は API 応答をそのまま保存し（加工・要約はしない）、`raw/source-catalog.edn` に CIK・企業名・取得日・SHA-256 を記録します。

## 今後（follow-up、未実装）

`cloud-murakumo-market-intel` の ingestion pipeline（週次クラウド routine でスケジュール済み）が、実行のたびにこのアーカイブへ生レスポンスを自動書き込みする配線は、このリポジトリの新設時点ではまだ実装されていません。別セッションでレビューしてから統合する予定の follow-up です。
