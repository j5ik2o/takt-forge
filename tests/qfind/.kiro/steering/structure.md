# Project Structure

## Organization Philosophy

フラットな `src/` 構成を維持しつつ、責務で分割するレイヤード寄りの設計です。  
「CLI 入力」「検索条件の型」「走査ポートと実装」「エラー契約」を別モジュールに分離し、依存方向を単純化しています。  
加えて、`main.rs`（実行境界）と `lib.rs`（再利用ロジック）を分け、プロセス制御と検索ロジックの責務を分離しています。

## Directory Patterns

### CLI Composition
**Location**: `/src/main.rs`, `/src/cli_input_adapter.rs`, `/src/search_command.rs`  
**Purpose**: 引数解釈と検索コマンド構築、および実行フローの組み立て  
**Example**: `CliInputAdapter::parse_from_iter(...) -> SearchCommand -> apply_conditions(...)`

### Crate Boundary
**Location**: `/src/main.rs`, `/src/lib.rs`  
**Purpose**: バイナリは標準入出力と終了コード制御を担当し、ライブラリは検索ロジックを公開する  
**Example**: `main::run(...) -> qfind::collect_candidate_paths(...)`

### Domain Primitives
**Location**: `/src/file_name_pattern.rs`, `/src/content_pattern.rs`, `/src/depth_limit.rs`  
**Purpose**: 入力条件を型として保持し、parse 時に検証を完了させる  
**Example**: `FileNamePattern::parse("*.rs".to_string())`

### Path Scanning Boundary
**Location**: `/src/path_scanner_port.rs`, `/src/parallel_path_scanner.rs`, `/src/scan_snapshot.rs`, `/src/scan_issue.rs`  
**Purpose**: 走査の抽象（Port）と実装（並列走査）を分離し、結果と問題情報を明示的に扱う  
**Example**: `PathScannerPort::scan_paths(...) -> ScanSnapshot`

### Contract Tests
**Location**: `/tests/*_contract.rs`  
**Purpose**: CLI と型の外部契約を統合テストで固定する  
**Example**: `tests/cli_input_contract.rs` で終了コード・JSON 形式・オプション挙動を検証

## Naming Conventions

- **Files**: `snake_case.rs`（責務名を直接表す）
- **Types / Traits**: `PascalCase`、trait は `*Port`、入出力境界は `*Adapter` を使用
- **Functions**: `snake_case` の動詞句（`parse`, `matches_path`, `collect_candidate_paths` など）
- **Error Variants**: `InvalidXxx`, `XxxFailed` で失敗種別を明示
- **Naming Rule**: 曖昧サフィックス（`Manager`, `Util`, `Service`, `Engine`, `Runtime`, `Facade`）は新規命名で避ける

## Import Organization

```rust
use std::path::PathBuf;          // 標準ライブラリ
use clap::Parser;                // 外部 crate
use crate::qfind_error::QfindError; // 自 crate
```

- 基本順序は `std` → 外部 crate → `crate::...`
- パスエイリアスは使わず、Rust 標準のモジュールパスを使用

## Code Organization Principles

- `main.rs` はオーケストレーションに専念し、ルール実装は各モジュールに委譲する
- `lib.rs` を検索ロジックの公開境界にし、`main.rs` からの依存方向を一方向に保つ
- parse 済み型を受け渡し、文字列のまま処理を続けない（不正状態の伝播を防止）
- 並列処理の結果は明示的にソートし、実行環境差による出力揺れを抑える
- 回復可能な失敗（権限拒否）はスキップ、回復不能な失敗は `QfindError` で即時返却する

## Sync Metadata

- updated_at: 2026-02-19T16:45:48Z
- change_reason: `src/lib.rs` を起点とした bin + lib 境界パターンを追記し、現行コード構造との整合性を補強
