# gov.sec.edgar

`data.sec.gov` の SEC EDGAR XBRL companyfacts API から取得した、米国 SEC 提出企業の生財務ファクト JSON snapshot を保全する DataLad dataset です。統合・要約用 fact（企業ごとの revenue/assets/net-income 等の要約 datom）は `gftdcojp/cloud-murakumo-market-intel` が生成します。

各 raw JSON は API 応答をそのまま保存し（加工・要約はしない）、`raw/source-catalog.edn` に CIK・企業名・取得日・SHA-256 を記録します。

## cloud-murakumo-market-intel からの自動連携（2026-07-15 実装済み）

`cloud-murakumo-market-intel` の ingestion pipeline（`market-intel.ingest`、
`clojure -M:feed:ingest`）は、実行時に環境変数 `GOV_SEC_EDGAR_ARCHIVE_DIR` が
このリポジトリのローカル checkout を指していれば、SEC EDGAR companyfacts を
取得するたびに（同じ HTTP レスポンスをそのまま再利用し、追加リクエストなしで）
この repo の `raw/companyfacts/CIK<cik>.json` へ生 JSON を書き込み、
`raw/source-catalog.edn` の該当 CIK エントリを実ファイルから再計算した
SHA-256 で upsert する（他の既存エントリは一切変更しない）。この書き込み
ステップ自体は `git add`/`commit`/`push` を行わない — landing は運用者/
呼び出し側スクリプトの責務。

2026-07-15、実際の SEC EDGAR API とこのリポジトリの sibling checkout に対して
既存 28 社分を再実行し、書き込み結果が byte-identical（git diff なし）である
ことと、`raw/source-catalog.edn` の全 28 エントリの SHA-256 が実ファイルと
一致することを確認済み（詳細は `gftdcojp/cloud-murakumo-market-intel` の該当
PR）。

## 既知の未実装 / follow-up

- B2 special remote（git-annex 経由の実体格納）は未確立（ADR-2607151950）。
- スケジュール済みの Claude Code cloud routine（`cloud-murakumo-market-intel`
  側、`trig_01CHhC7b6FvhP5YXNv4GvgA1`）は、現時点ではこのリポジトリを
  sources に含めておらず、`GOV_SEC_EDGAR_ARCHIVE_DIR` も設定していない —
  routine 側の追従設定は別途必要（RemoteTrigger API 経由、別セッションの
  scope）。
