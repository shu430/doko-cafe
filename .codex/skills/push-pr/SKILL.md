---
name: push-pr
description: Use this skill in the doko-cafe repository after commits are complete, when Codex needs to verify the working tree, push the current development branch to the remote, and create or prepare a Pull Request to the repository default branch. It must block direct pushes to default branches such as main or develop, avoid force push unless explicitly instructed, and produce a clear PR title, description, evidence, and checklist.
---

# doko-cafe Push / Pull Request Skill

## 目的

この Skill は、現在の開発 branch を remote branch へ push し、default branch へ向けた Pull Request を作成するために使用する。

## 使用する場面

以下の場合にこの Skill を使用する。

- commit 済みの開発 branch を remote へ push する。
- push 前に作業ツリー、commit、テスト結果を確認する。
- 現在の開発 branch から default branch への Pull Request を作成する。
- Pull Request の description を整理する。
- レビュー依頼前のセルフチェックを行う。

## 前提ルール

- 作業前に `AGENTS.md` を確認する。
- commit 作成は `commit` Skill の責務とする。
- この Skill では、原則として新しい commit を作成しない。
- push 前に作業ツリーが clean であることを確認する。
- push 前に現在の branch が開発 branch であることを確認する。
- default branch に直接 push しない。
- `main` または `develop` で push しない。
- Pull Request の向きは、現在の開発 branch から default branch にする。
- default branch 名は、repository の設定に従う。
- default branch が不明な場合は `main` を第一候補、`master` を第二候補とする。
- `git push --force`、`git push --force-with-lease` は、明示的な指示がない限り実行しない。
- API key、secret、connection string、個人情報を PR description に記載しない。
- typecheck、lint、test の失敗を隠さない。
- 未確認事項や残リスクがある場合は、PR description に明記する。

## 作業手順

### 1. 現在の状態を確認する

最初に以下を実行する。

```bash
git status
git branch --show-current
git log --oneline -5
```

確認する内容:

- 現在の branch
- 作業ツリーが clean か
- staged file、unstaged file、untracked file が残っていないか
- 直近 commit が意図した内容か
- default branch 上で作業していないか
- `main` または `develop` 上で作業していないか

作業ツリーが clean でない場合は、push せずに状況を報告する。

### 2. remote との差分を確認する

以下を実行する。

```bash
git fetch origin
git status
```

必要に応じて、現在の branch と upstream の差分を確認する。

```bash
git log --oneline --decorate --graph --all -20
```

確認する内容:

- remote branch が存在するか
- local branch が remote branch より進んでいるか
- remote branch 側に未取り込みの commit がないか
- 意図しない diverge が発生していないか

remote branch と diverge している場合は、push せずに状況を報告する。

### 3. push 前の確認コマンドを実行する

push 前に、利用可能な範囲で以下を実行する。

```bash
npm run typecheck
npm run lint
npm test
npm run build
```

実行方針:

- TypeScript を変更した場合は、`npm run typecheck` を実行する。
- code style や ESLint 対象の変更がある場合は、`npm run lint` を実行する。
- 実装や設定を変更した場合は、`npm run build` を実行する。
- logic、service、utils、Component、custom hook を変更した場合は、関連 test を実行する。
- test script が存在する場合は、`npm test` を実行する。
- `npm run typecheck` は script が存在する場合のみ実行する。
- docs のみの変更の場合は、確認コマンドは未実行でよい。
- 未実行理由は「docs のみの変更のため不要」とする。
- コマンドが存在しない場合、または実行できない場合は、その理由を説明する。

確認結果は PR description の `Evidence` に記載する。

### 4. push する

現在の開発 branch を remote へ push する。

remote branch が未作成の場合:

```bash
git push -u origin <current-branch>
```

remote branch が作成済みの場合:

```bash
git push origin <current-branch>
```

push 後に以下を確認する。

```bash
git status
```

確認する内容:

- push が成功したか
- local branch と remote branch が同期しているか
- 作業ツリーが clean か

### 5. PR 作成前に変更内容を整理する

default branch を確認し、以下を実行する。

```bash
git log --oneline <default-branch>..<current-branch>
git diff --stat <default-branch>..<current-branch>
```

必要に応じて以下も確認する。

```bash
git diff <default-branch>..<current-branch>
```

確認する内容:

- PR に含まれる commit
- PR に含まれる変更ファイル
- 変更内容と影響範囲
- 不要な変更が含まれていないか
- API key、secret、connection string が含まれていないか
- debug log や不要ファイルが含まれていないか

### 6. PR title を作成する

PR title は、変更の主目的が分かる名前にする。

形式:

```text
<type>(<scope>): <summary>
```

例:

```text
feat(cafe-search): add cafe search flow
fix(google-maps): handle api error display
docs(agents): add push pr skill
refactor(route-search): split route search service
```

複数 commit が含まれる場合は、PR 全体の目的を表す title にする。

### 7. PR description を作成する

PR description は以下の形式で作成する。

```markdown
## Why

- なぜこの変更が必要かを記載する。
- 背景、目的、解決したい課題を記載する。

## What

- 何を変更したかを記載する。
- 変更した機能、画面、Component、service、utils、types、設定を記載する。
- 影響範囲を記載する。

## Evidence

- 実施した確認内容を記載する。
- 実行したコマンドと結果を記載する。
- 必要に応じてスクリーンショット、動画、ログ、動作確認内容を記載する。

例:

- `npm run typecheck`: passed
- `npm run lint`: passed
- `npm test`: passed
- `npm run build`: passed
- 画面表示を手動確認済み
- Google Maps リンクが開けることを確認済み

## Checklist

- [ ] セルフレビューを実施した。
- [ ] 不要な debug log、`console.log`、コメントを削除した。
- [ ] API key、secret、connection string が含まれていないことを確認した。
- [ ] 変更範囲が PR の目的と一致している。
- [ ] 既存機能への影響を確認した。
- [ ] 必要な typecheck、lint、test、build を実行した。
- [ ] 未確認事項や残リスクを記載した。

## Others

- 補足事項を記載する。
- レビューで重点的に見てほしい点を記載する。
- 判断に迷った点、trade-off、今後の課題を記載する。
```

## PR description 作成ルール

### Why

`Why` には、変更理由を書く。

良い例:

- カフェ検索結果を画面に表示するため。
- Google Maps API error 時に画面が壊れる問題を防ぐため。
- 実装・テスト時の AI 作業ルールを明確化するため。

悪い例:

- 必要だから。
- 修正したかったため。
- なんとなく。

### What

`What` には、変更内容と影響範囲を書く。

記載対象:

- 追加した機能
- 修正した機能
- 変更した Component
- 変更した service
- 変更した utility function
- 変更した type
- 変更した設定
- 影響を受ける画面や操作

### Evidence

`Evidence` には、確認内容と結果を書く。

記載対象:

- 実行したコマンド
- コマンド結果
- 手動確認内容
- スクリーンショット
- 動画
- ログ
- 未実施の理由

例:

```markdown
- `npm run typecheck`: passed
- `npm run lint`: passed
- `npm test`: failed
  - 理由: 既存の unrelated test が失敗しているため
  - 対応: 本 PR の変更箇所には影響なしと判断
```

失敗や未実施を隠さない。

### Checklist

`Checklist` には、レビュー前の確認事項を書く。

必須項目は、PR description template の `Checklist` に記載した項目を使用する。

### Others

`Others` には、補足を書く。

記載対象:

- レビューで重点的に見てほしい点
- 懸念事項
- trade-off
- 今回対応しないこと
- 次回以降の課題

## PR 作成

GitHub CLI が利用可能な場合は、以下で PR を作成する。

```bash
gh pr create \
  --base <default-branch> \
  --head <current-branch> \
  --title "<pr-title>" \
  --body-file <pr-body-file>
```

GitHub CLI が利用できない場合は、PR title と PR description を出力し、Web UI で作成できる状態にする。

## push / PR 作成時の禁止事項

以下は禁止する。

- default branch に直接 push する。
- `main` または `develop` に直接 push する。
- 作業ツリーが clean でない状態で push する。
- 意図しない差分を含めたまま push する。
- `git push --force`、`git push --force-with-lease` を明示的な指示なく実行する。
- typecheck、lint、test の失敗を隠す。
- 実行していない確認を「実行済み」と書く。
- API key、secret、connection string、個人情報を PR description に記載する。
- `Why`、`What`、`Evidence` が空の PR を作成する。
- 変更内容と関係ない PR title を付ける。
- 未確認事項や残リスクを隠す。

## 完了時の出力

push / PR 作成後は、以下を簡潔に示す。

- push した branch
- 実行した push command
- Pull Request の base branch と head branch
- PR title
- PR URL、または Web UI で作成するための PR title / description
- 実行した確認コマンドと結果
- 未確認事項や残リスク
