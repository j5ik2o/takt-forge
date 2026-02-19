# Technology Stack

## Architecture

Rust 製の CLI（`src/main.rs` のバイナリ + `src/lib.rs` のライブラリ構成）。  
ライブラリ層に検索ロジックを集約し、バイナリ層は入出力と終了コード制御に集中します。  
処理は「入力適合（CLI 引数）→ 候補収集（並列パス走査）→ 条件適用（glob / regex）→ 出力（text / JSON）」の直列パイプラインで構成されています。  
I/O 境界とドメインロジックを分離するため、`PathScannerPort`（trait）と `ParallelPathScanner`（実装）を分ける構造を採用しています。

## Core Technologies

- **Language**: Rust (edition 2024)
- **Framework**: なし（標準ライブラリ + crate 組み合わせ）
- **Runtime**: ネイティブ CLI バイナリ

## Key Libraries

- **clap**: CLI 引数の宣言的パース
- **ignore**: `.gitignore` を考慮したディレクトリ走査
- **rayon**: 並列処理（走査・ソート）
- **globset**: ファイル名 glob マッチング
- **regex**: ファイル内容の正規表現マッチング
- **serde / serde_json**: JSON 出力整形
- **thiserror**: エラー型の定義と表示の一元化

## Development Standards

### Type Safety

- CLI 文字列は早期に `FileNamePattern` / `ContentPattern` / `DepthLimit` に parse して保持する
- オプション条件は `Option<T>` で表現し、未指定状態を明示する
- 実行時の判定分岐は `QfindError` に集約して、失敗の意味を型で区別する

### Code Quality

- エラーは `QfindError` で統一し、終了コードを `exit_code()` で一元管理する
- 並列収集後にソートし、出力順序の再現性を保証する
- Permission denied は継続可能な失敗として扱い、全体停止を避ける

### Testing

- ユニットテストを各モジュール内（`#[cfg(test)]`）で実施
- CLI 振る舞いは `tests/*_contract.rs` の統合テストで契約として検証
- JSON 出力、深さ制限、`.gitignore` 尊重、権限エラー継続を回帰対象に含める

## Development Environment

### Required Tools

- Rust toolchain（edition 2024 対応）
- Cargo

### Common Commands

```bash
# Dev
cargo run -- <search_root> [--glob '*.rs'] [--regex 'pattern'] [--max-depth 2] [--json]

# Build
cargo build

# Test
cargo test
```

## Key Technical Decisions

- `ignore::WalkBuilder` の設定で `.gitignore` は有効化しつつ、他の ignore ソースは無効化して挙動を明確化
- 走査とフィルタを分離し、探索対象の収集責務を `ParallelPathScanner` に限定
- JSON 形式は `[{ "path": "..." }]` に固定し、外部連携時の取り回しを優先
- 入力不正は終了コード `2`、実行失敗は `1` とし、CLI 契約を明確にした
- `main.rs`（プロセス境界）と `lib.rs`（再利用可能な検索ロジック）を分離し、責務の混在を防いだ

## Sync Metadata

- updated_at: 2026-02-19T16:45:48Z
- change_reason: 実コードが bin + lib の2ターゲット構成である点を steering に反映し、構造記述の乖離を解消
