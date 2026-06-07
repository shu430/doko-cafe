---
name: commit
description: Use this skill for Git operations in the doko-cafe repository, especially checking status, preparing logically split Conventional Commits, reviewing diffs, and branch merge preparation. Do not perform destructive Git operations without explicit user instruction.
---

# doko-cafe Git 操作 Skill

## 目的

この Skill は、現在の変更差分を確認し、論理的な単位に分割したうえで、Conventional Commits に則って commit を作成するために使用する。

## 使用する場面

以下の場合にこの Skill を使用する。

- 作業中の変更差分を確認する。
- commit 対象のファイルを整理する。
- 変更内容を意味のある単位に分割する。
- Conventional Commits に則った commit message を作成する。
- commit 前に不要な差分や機密情報が含まれていないか確認する。
- 複数 commit に分けて履歴を整理する。
- ブランチマージ準備として未 commit の変更や注意点を整理する。

## 前提ルール

- 作業前にリポジトリルートの `AGENTS.md` を確認する。
- commit 前に必ず `git status` と `git diff` を確認する。
- 現在変更されているファイルを、論理的な単位・意味的なまとまりに分割して commit する。
- 原則として、すべての変更をまとめて 1 つの commit にしない。
- `git add .` は原則として使用しない。
- staging はファイル単位、または必要に応じて hunk 単位で行う。
- commit message は Conventional Commits に従う。
- API key、secret、connection string、個人情報などの機密情報を commit しない。
- debug log、不要な `console.log`、一時ファイル、生成物を commit しない。
- 自分が変更していない差分を勝手に戻さない。
- 無関係な変更は触らず、必要に応じてユーザーに説明する。
- 破壊的な Git 操作は、明示的な指示がない限り実行しない。
- 現在の branch が `main` または `develop` の場合、現在の branch から作業 branch を作成してから commit 作業を進める。
- 作業 branch 名は、`git diff` 実行後に変更内容を確認してから命名する。
- 現在の branch が `main` または `develop` の場合、push を実行しない。
- commit 後に push が必要な場合は、`push-pr` Skill の手順に従う。

## commit 単位の考え方

commit は、以下のような意味的なまとまりで分割する。

| 単位 | 例 |
| --- | --- |
| 新機能追加 | カフェ検索 service を追加する。 |
| バグ修正 | Google Maps API error 時の表示を修正する。 |
| リファクタリング | Google Maps URL 生成処理を `services/googleMaps/` に分離する。 |
| テスト追加 | ルート URL 生成処理の UT を追加する。 |
| ドキュメント修正 | `AGENTS.md` のテスト方針を更新する。 |
| 設定変更 | ESLint 設定を変更する。 |

以下のような commit は避ける。

- 複数の無関係な変更を 1 つにまとめた commit
- `fix`、`update`、`修正` だけの commit
- 実装、テスト、ドキュメント、設定変更が無秩序に混ざった commit
- 不要ファイルや一時ファイルを含む commit

## Conventional Commits の形式

commit message は以下の形式にする。

```text
<type>(<scope>): <日本語の説明>
```

`scope` は任意とする。ただし、変更範囲が明確な場合は付与する。

例:

```text
feat(cafe-search): カフェ検索 service を追加
fix(google-maps): api error response の処理を修正
refactor(route): ルート URL 生成処理を分離
test(utils): ルート URL 生成処理のテストを追加
docs(agents): Git commit ルールを更新
chore(devcontainer): 開発環境設定を更新
```

### type の使い分け

| type | 用途 |
| --- | --- |
| `feat` | 新機能追加 |
| `fix` | バグ修正 |
| `refactor` | 振る舞いを変えない内部改善 |
| `test` | テスト追加・修正 |
| `docs` | ドキュメント修正 |
| `chore` | ビルド、設定、依存関係、補助的な変更 |
| `style` | フォーマット、空白、CSS など、ロジックに影響しない変更 |
| `perf` | performance 改善 |
| `ci` | CI 設定変更 |
| `build` | build system、package、依存関係に関する変更 |
| `revert` | 変更の取り消し |

### scope の考え方

`scope` には、変更対象の機能、領域、ディレクトリ、責務を指定する。

| scope | 用途 |
| --- | --- |
| `cafe-search` | カフェ検索機能 |
| `route-search` | ルート検索機能 |
| `google-maps` | Google Maps API 連携 |
| `components` | 共通 Component |
| `hooks` | custom hook |
| `services` | service 層 |
| `utils` | utility function |
| `types` | 型定義 |
| `agents` | `AGENTS.md` や Skill |
| `devcontainer` | DevContainer 設定 |

### commit message 作成ルール

- `type` と `scope` は Conventional Commits に従い英語表記にする。
- `description` は日本語で、変更内容を具体的に書く。
- header 全体の形式は `<type>(<scope>): <日本語の説明>` とする。
- 破壊的変更がある場合は `!` または footer の `BREAKING CHANGE:` を使用する。
- 複数の意味を含む message にしない。

良い commit message:

```text
feat(cafe-search): カフェ検索 service を追加
fix(google-maps): ルート検索 error の処理を修正
refactor(utils): Google Maps URL 生成処理を分離
test(route-search): ルート検索 validation のテストを追加
docs(agents): Git commit Skill を追加
chore(devcontainer): Node.js version を更新
```

悪い commit message:

```text
fix
update
修正
いろいろ変更
対応
wip
```

## 作業手順

### 1. 現在の状態を確認する

最初に以下を実行する。

```bash
git status
git branch --show-current
git diff --stat
```

確認する内容:

- 現在の branch
- unstaged file
- staged file
- untracked file
- 変更量
- 意図しないファイル変更の有無

### 2. main / develop の場合は作業 branch を作成する

現在の branch が `main` または `develop` の場合、commit 作業を続ける前に以下を実行し、変更内容を確認する。

```bash
git diff
git diff --staged
```

確認した変更内容をもとに、作業内容が分かる branch 名を決める。

branch 名の例:

```text
docs/update-commit-skill
feat/cafe-search
fix/google-maps-error
```

branch 名を決めた後、現在の branch から作業 branch を作成する。

```bash
git switch -c <branch-name>
```

`<branch-name>` には、変更内容を確認して決めた branch 名を使用する。

現在の branch が `main` または `develop` 以外の場合は、branch を作成せず次の手順に進む。

### 3. 差分を確認する

以下を実行する。

```bash
git diff
git diff --staged
```

確認する内容:

- 変更内容が意図通りか
- commit すべき変更か
- 不要な debug log がないか
- API key、secret、connection string が含まれていないか
- 一時ファイル、生成物、不要ファイルが含まれていないか
- package lock file の変更が妥当か

### 4. commit 単位を設計する

現在の変更を確認し、意味的なまとまりごとに commit 単位を決める。

出力例:

```markdown
## commit 分割案

### commit 1
- 目的: カフェ検索 service を追加する
- 対象ファイル:
  - `services/googleMaps/searchCafes.ts`
  - `types/cafe.ts`
- commit message:
  - `feat(cafe-search): カフェ検索 service を追加`

### commit 2
- 目的: カフェ検索結果の表示 Component を追加する
- 対象ファイル:
  - `components/CafeCard.tsx`
  - `components/CafeList.tsx`
- commit message:
  - `feat(cafe-search): カフェ検索結果 Component を追加`

### commit 3
- 目的: カフェ検索 service のテストを追加する
- 対象ファイル:
  - `app/tests/searchCafes_test.ts`
- commit message:
  - `test(cafe-search): カフェ検索 service のテストを追加`
```

### 5. staging する

commit 単位ごとに staging する。

ファイル単位で staging する場合:

```bash
git add <file>
```

hunk 単位で staging する場合:

```bash
git add -p <file>
```

`git add .` は原則として使用しない。
使用する場合は、すべての差分を確認し、不要ファイルが含まれないことを確認してから行う。

### 6. staged 差分を確認する

commit 前に必ず以下を実行する。

```bash
git diff --staged
git status
```

確認する内容:

- staged file が commit 単位と一致しているか
- 無関係な変更が混ざっていないか
- 機密情報が含まれていないか
- debug log や不要ファイルが含まれていないか

### 7. 必要な確認コマンドを実行する

可能な場合、commit 前に以下を実行する。

```bash
npm run typecheck
npm run lint
npm test
npm run build
```

- `npm run typecheck` は script が存在する場合のみ実行する。
- `npm test` は script が存在する場合のみ実行する。
- 変更内容が docs のみの場合、確認コマンドは未実行でよい。
- 未実行理由は「docs のみの変更のため不要」とする。

### 8. commit する

Conventional Commits に則って commit する。

```bash
git commit -m "<type>(<scope>): <日本語の説明>"
```

例:

```bash
git commit -m "feat(cafe-search): カフェ検索 service を追加"
```

### 9. 複数 commit を作成する

複数の論理単位がある場合は、以下を繰り返す。

- 次の commit 対象を staging する。
- `git diff --staged` で確認する。
- 必要に応じて typecheck、lint、test を実行する。
- Conventional Commits に則って commit する。
- commit 後に `git status` を確認し、残っている変更が次の commit 対象か、意図的に未 commit なのかを整理する。

## ブランチマージ準備

- merge 前に `git status` を確認する。
- 未 commit の変更、未追跡ファイル、テスト未実行項目を確認する。
- merge に必要な前提や注意点を整理する。

## 禁止事項

- 差分確認をせずに commit しない。
- すべての変更を無条件に `git add .` しない。
- 無関係な変更を 1 つの commit に混ぜない。
- Conventional Commits に従わない commit message を作成しない。
- `fix`、`update`、`修正` だけの commit message を使わない。
- API key、secret、connection string を commit しない。
- debug log、一時ファイル、不要な生成物を commit しない。
- typecheck、lint、test の失敗を無視して commit しない。
- ユーザーの明示指示なしに merge、rebase、reset 系、`git checkout --`、clean 系、force push を実行しない。
- `main` または `develop` で commit しない。
- 自分が作成していない変更を勝手に削除しない。

## 完了時の出力

Git 操作後は、以下を簡潔に示す。

- 実行した Git コマンド
- 現在のブランチ状態
- 変更ファイル
- commit や merge に向けた注意点
