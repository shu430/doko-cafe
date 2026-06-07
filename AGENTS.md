# doko-cafe

## Project Overview（プロジェクト概要）

このプロジェクトは、Next.js を使用した Web アプリケーションである。
Google Maps API を利用して、ルート検索、地図表示、カフェ検索、Google Maps リンク生成を行う。
旅行ルート沿いのカフェを探したい場合に利用する。

ユーザー入力:
- origin: 出発地
- destination: 目的地
- transport mode: 移動手段（walking | driving | bicycle）
  - Phase1 では `walking` のみ有効とする。
  - `driving` / `bicycle` は将来対応とする。

アプリケーションは、指定されたルート周辺のカフェを検索する。

---

## Tech Stack（技術構成）

- Next.js: ルーティング、ページ描画、Server Component / Client Component の構成を担当する。
- TypeScript: components、services、utils、types の型安全性を担保する。
- Tailwind CSS: UI のスタイリングとレスポンシブレイアウトを担当する。
- Google Maps API: ルート検索、地図表示、カフェ検索、Google Maps リンク生成を担当する。
- DevContainer: ローカル開発環境の統一を担当する。

---

## Language Policy（記述言語ルール）

- AGENTS.md と docs/rules.md は基本的に日本語で記述する。
- TypeScript、Node.js、Next.js、React、Tailwind CSS、Google Maps API などの技術名・ライブラリ名は正式表記を使用する。
- コード、ファイル名、ディレクトリ名、関数名、型名、コマンドは英語表記を使用する。
- 曖昧な表現を避け、禁止事項・推奨事項・完了条件を明確に書く。

---

## Additional Rules（追加ルール）

- 詳細なコーディング規約と命名規約は `docs/rules.md` を参照する。
- 実装時は `AGENTS.md` と `docs/rules.md` の両方に従う。
- ルールが競合する場合は、より具体的な指示を優先する。
- `docs/rules.md` が長くなりすぎた場合は、重複や古いルールを整理する。

---

## Skills（スキル）

- このリポジトリ専用の Skill は `.codex/skills/` に配置する。
- Skill は必要に応じてスラッシュコマンドで明示的に呼び出す。
- 要件整理・計画作成では `.codex/skills/plan/SKILL.md` を使用する。
- 実装・テストでは `.codex/skills/impl/SKILL.md` を使用する。
- Git 操作やブランチマージ準備では `.codex/skills/commit/SKILL.md` を使用する。
- push / Pull Request 作成では `.codex/skills/push-pr/SKILL.md` を使用する。
- Skill の内容が `AGENTS.md` や `docs/rules.md` と競合する場合は、ユーザーの最新指示を最優先し、その次により具体的なルールを優先する。

---

## Directory Rules（ディレクトリ責務）

```text
app/
  Next.js の route、layout、page を配置する。

components/
  複数箇所で再利用する UI Component を配置する。

features/
  feature 固有の Component、custom hook、logic、型を配置する。

services/
  外部 API アクセスや外部サービス連携を配置する。
  Google Maps API 関連のロジックは services/googleMaps/ に集約する。

utils/
  純粋関数の helper を配置する。

types/
  複数箇所で使用する共通型を配置する。

constants/
  複数箇所で使用する定数を配置する。

config/
  アプリ設定、環境変数名、外部サービス設定の定義を配置する。
  secret 値は配置しない。
```

---

## Development Policy（開発方針）

まず MVP を実装する。

Phase1:
- walking のみ対応
- 地図表示
- カフェ一覧
- Google Maps リンク

---

## Commands（実行コマンド）

開発サーバーを起動する:

```bash
npm run dev
```

---

## Prohibited（禁止事項）

- secret、API キー、`.env*` の値を commit しない。
- 現時点では Redux、Zustand、その他の global state library を追加しない。
- Google Maps API の呼び出しを `services/googleMaps/` 以外に配置しない。
- 明確な理由があり、その近くに説明がある場合を除き、`any` を使用しない。
- 不要な `console.log`、debug UI、dead code を残さない。
- MVP タスクの実装時に無関係な refactor を行わない。

---

## Test Commands（テストコマンド）

完了前に実行する:

```bash
npm run lint
npm run build
```

`npm run typecheck` は script が存在する場合のみ実行する。
`npm test` は script が存在する場合のみ実行する。
docs のみの変更では確認コマンドを未実行にしてよい。
未実行理由は「docs のみの変更のため不要」とする。

現時点では専用の `npm test` script は存在しない。
自動テストを導入した時点で追加する。

---

## Completion Criteria（完了条件）

- 依頼された挙動が実装され、Phase1 の範囲で動作する。
- UI ロジックと外部 API ロジックが分離されている。
- `npm run lint` が成功する。
- `npm run build` が成功する。
- API キー、secret、不要なログ、無関係な変更が含まれていない。

---

## Maintenance Policy（メンテナンス方針）

- 会話の中で繰り返し指示されたことがある場合は、AGENTS.md または docs/rules.md への反映を検討する。
- 冗長なルールや圧縮できる箇所を定期的に見直す。
- 簡潔でありながら密度の濃い文書にする。
