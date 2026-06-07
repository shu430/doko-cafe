---
name: doko-cafe-planning
description: Use this skill for doko-cafe requirements clarification and planning before implementation. This includes clarifying scope, identifying files, separating responsibilities, and creating an implementation plan.
---

# doko-cafe Requirement / Design Skill

## 目的

この Skill は、実装前に要件、仕様、設計方針、責務分担、影響範囲、テスト観点を整理するために使用する。

## 使用する場面

以下の場合にこの Skill を使用する。

- 新機能の要件を整理する。
- 既存機能の仕様を確認する。
- 画面、Component、service、API、DB、外部API連携の責務を整理する。
- 実装前に設計方針を決める。
- 設計レビューを行う。
- ADR に残す内容を整理する。

## 前提ルール

- 作業前にリポジトリルートの `AGENTS.md` と `docs/rules.md` を読む。
- 不明点、前提、制約を明確にする。
- 要件と設計を混同しない。
- ユーザー価値、業務ルール、技術制約を分けて考える。
- 技術名、ライブラリ名、ファイル名、関数名、型名は正式表記を使用する。
- 推測で仕様を決めない。推測する場合は「推測」と明記しplanファイルに記録しユーザに確認する。

## 入力として確認する情報

作業時は、可能な範囲で以下を確認する。

- 実現したいこと
- 対象ユーザー
- 画面や機能の利用シナリオ
- 入力項目
- 出力項目
- 正常系
- 異常系
- 権限、認証、認可の要否
- 外部API利用の有無
- Google Maps API 利用の有無
- 既存コードへの影響範囲
- 非機能要件
- performance
- security
- accessibility
- maintainability

## 作業手順

### 1. 要件を整理する

以下の観点で整理する。

- この機能で何を実現するのか
- 誰が使うのか
- どの画面、操作、データに関係するのか
- 何を入力し、何を出力するのか
- 成功条件は何か
- 対象外とすることは何か

### 2. 仕様を整理する

以下を整理する。

- 正常系の動作
- 異常系の動作
- 空データ時の動作
- loading 時の動作
- API error 時の動作
- validation rule
- 表示文言
- ユーザー操作後の状態変化

### 3. 設計方針を整理する

`AGENTS.md` と `docs/rules.md` の責務定義を前提に、今回の変更に関係する配置先と責務のみを整理する。
計画では以下の区分を使い、各区分に置く理由と境界を明確にする。

- `app/`
- `components/`
- `features/`
- `services/`
- `utils/`
- `types/`
- `constants/`
- `config/`

### 4. 型を設計する

以下を整理する。

- 入力用の型
- 表示用の型
- service の戻り値の型
- 外部APIレスポンスからアプリ内部用の型への変換方針

例:

```ts
type CafeSearchParams = {
  keyword: string;
  location: LocationPoint;
  radiusMeters: number;
};

type CafeSearchResult = {
  id: string;
  name: string;
  address: string;
  rating?: number;
  location: LocationPoint;
};
```

### 5. テスト観点を整理する

以下を整理する。

- 正常系
- 異常系
- 境界値
- 空データ
- API error
- validation error
- UI 表示
- service の変換処理
- utility function の純粋性

### 6. リスクを整理する

以下を整理する。

- 技術的なリスク
- 仕様の曖昧さ
- Google Maps API の quota / cost
- API key の公開範囲
- performance への影響
- 既存機能への影響

### 7. 計画ファイルを作成する

計画作成後、`/workspace/docs/plan/` に plan ファイルを作成する。
ディレクトリが存在しない場合は作成する。

- ファイル名は `yyyyMMddhhmmss_<plan-title>.md` とする。
- timestamp は 24 時間表記で `date +%Y%m%d%H%M%S` を使用する。
- `<plan-title>` は内容が分かる短い英語の kebab-case にする。
- 例: `20260607123045_route-cafe-search.md`

## plan ファイルの出力形式

````md
# <Plan Title>

## Metadata

- Created: yyyy-MM-dd HH:mm:ss
- Skill: doko-cafe Requirement / Design Skill
- Status: Draft

## Requirement

- Goal:
- User:
- Scenario:
- Inputs:
- Outputs:
- Success Criteria:
- Out of Scope:

## Specification

- Normal Flow:
- Error Flow:
- Empty State:
- Loading State:
- API Error:
- Validation:
- UI Text:
- State Changes:

## Design

- Related Areas:
- Responsibility Split:
- Data Flow:
- External API:
- Google Maps API:

## Types

- Input Types:
- View Types:
- Service Return Types:
- Mapping Policy:

## Impact

- Files:
- Existing Behavior:
- Migration:

## Test Points

- Normal:
- Error:
- Boundary:
- Empty Data:
- API Error:
- Validation Error:
- UI:
- Service Mapping:
- Utility:

## Risks

- Technical:
- Specification:
- Google Maps API Quota / Cost:
- API Key Exposure:
- Performance:
- Existing Features:

## Implementation Steps

1. 

## Verification Commands

```bash
npm run lint
npm run build
```

`npm run typecheck` は script が存在する場合のみ実行する。
`npm test` は script が存在する場合のみ実行する。
docs のみの変更では確認コマンドを未実行にしてよい。
未実行理由は「docs のみの変更のため不要」とする。
````

## 完了時の出力

計画作成後は、以下を簡潔に示す。

- 作成した plan ファイル
- 整理した要件の要約
- 前提や未確定事項
- 影響範囲
- 実装手順の要約
- 確認コマンド
