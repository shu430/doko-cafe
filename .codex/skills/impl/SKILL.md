---
name: doko-cafe-implementation
description: Use this skill when implementing and testing changes in the doko-cafe repository. This includes coding, separating UI and API logic, running lint/build/test, and reporting implementation results.
---

# doko-cafe 実装・テスト Skill

この Skill は、設計済みの内容をもとに、Next.js / TypeScript アプリケーションの実装、テスト、軽微なリファクタリングを安全に進めるために使用する。

## 使用する場面

以下の場合にこの Skill を使用する。

- 新機能を実装する。
- 既存機能を修正する。
- Component を追加・修正する。
- custom hook を追加・修正する。
- service 関数を追加・修正する。
- utility function を追加・修正する。
- テストを追加・修正する。
- TypeScript error を解消する。
- 小規模なリファクタリングを行う。

## 前提ルール

- 作業前にリポジトリルートの `AGENTS.md` と `docs/rules.md` を確認する。
- 既存のディレクトリ構成、命名規約、コーディングルールに従う。
- 変更は小さく、責務を明確にする。
- 不要な抽象化を追加しない。
- 既存の設計方針を勝手に変更しない。
- 既存の public API、props、型を変更する場合は影響範囲を確認する。
- `any` は原則として使用しない。
- API key、secret、connection string を出力しない。
- 判断に迷う場合は、単純な実装を優先し、trade-off を説明する。

## 実装手順

### 1. 変更範囲を確認する

実装前に以下を確認する。

- 修正対象の機能
- 変更するファイル
- 追加するファイル
- 影響を受ける Component
- 影響を受ける custom hook
- 影響を受ける service
- 影響を受ける type
- 影響を受ける test
- 既存機能への影響

変更範囲が大きい場合は、作業を小さな単位に分割する。

### 2. 配置先を決める

配置先は `AGENTS.md` のディレクトリ責務に従う。

特に以下を守る。

- route、page、layout は `app/` に配置する。
- 再利用可能な UI Component は `components/` に配置する。
- feature 固有の Component、custom hook、logic は `features/` に配置する。
- 外部 API アクセスは `services/` に配置する。
- Google Maps API へのアクセスは `services/googleMaps/` に配置する。
- 純粋関数は `utils/` に配置する。
- 共通型は `types/` に配置する。
- 複数箇所で使用する定数は `constants/` に配置する。
- アプリ設定、環境変数名、外部サービス設定の定義は `config/` に配置する。secret 値は配置しない。

### 3. 実装する

#### Component

- Component 名、Component ファイル名は PascalCase にする。
- props の型名は `{ComponentName}Props` にする。
- Component は表示責務を中心にする。
- presentational component では API 呼び出しを行わない。
- Google Maps API の生レスポンスを props として受け取らない。
- Client Component は必要最小限にする。
- `'use client'` は必要な場合のみ付与する。

#### custom hook

- custom hook 名は必ず `use` で始める。
- hook は loop、condition、nested function、try/catch/finally の中で呼び出さない。
- 1 つの hook に複数の無関係な責務を持たせない。
- ブラウザ API を扱う再利用処理は custom hook に分離する。

#### service

- service 関数には戻り値の型を明示する。
- 外部 API レスポンスをアプリ内部用の型に変換して返す。
- Google Maps API の生レスポンスを UI Component に直接渡さない。
- quota error、API error、network error を明示的に処理する。
- API key は environment variable から取得する。
- API key、secret、connection string を log や出力に含めない。

#### utility function

- utility function は純粋関数にする。
- React に依存させない。
- 外部 API を呼び出さない。
- 関数名は、変換・計算・生成内容が分かる名前にする。

### 4. TypeScript を確認する

以下を確認する。

- `any` を使っていないか。
- `unknown` を適切に型 narrowing しているか。
- export 関数に戻り値の型があるか。
- Component props の型が明確か。
- 外部 API レスポンス型とアプリ内部型が分離されているか。
- 不要な型アサーションがないか。
- 型エラーを握りつぶしていないか。

## テスト方針

このプロジェクトでは、以下の試験区分でテストを整理する。

テストを作成するフォルダは `app/tests/` とする。
テストファイル名には `_test` を付け、`xxx_test.xxx` の形式にする。

| 試験区分 | 目的 | 実施方針 |
| --- | --- | --- |
| UT | 関数、Component、service、utility function など、小さい単位の正しさを確認する。 | 実施する。 |
| ITa | アプリ内部の Component、custom hook、service、utils の連携を確認する。 | 実施する。 |
| ITb | Google Maps API など、外部システムとの実連携を確認する。 | 原則として自動テストでは実施しない。必要時のみ手動で疎通確認する。 |
| ST | 画面や機能をまたいだシナリオ全体を確認する。 | 今回は原則として独立工程では実施しない。必要な観点は UAT に含める。 |
| UAT | ユーザー目線で、機能が受け入れ可能か確認する。 | 最小限のチェックリストで実施する。 |

### UT: 単体テスト

UT では、以下を確認する。

- `utils/` の純粋関数が、入力に対して期待する出力を返すこと。
- `services/` の変換処理が、外部 API レスポンスをアプリ内部用の型へ正しく変換すること。
- Component が props に応じて正しく表示されること。
- custom hook が状態変化、loading、error を正しく扱うこと。
- Google Maps API の実通信は行わず、必要に応じて mock を使用すること。

UT の対象例:

- `formatDistance()`
- `formatDuration()`
- `buildGoogleMapsRouteUrl()`
- `buildGoogleMapsPlaceUrl()`
- `mapGooglePlaceToCafeSearchResult()`
- `CafeCard`
- `RouteSummary`

### ITa: システム内部結合テスト

ITa では、アプリ内部の連携を確認する。

確認対象:

- 入力 Component と検索処理の連携
- custom hook と service の連携
- service と mapper の連携
- 検索結果の表示
- loading state の表示
- error state の表示
- 空データ時の表示

ITa では、Google Maps API の実通信は行わない。
外部 API レスポンスは mock し、アプリ内部の連携確認に集中する。

### ITb: システム外部結合テスト

ITb は、Google Maps API など外部システムとの実連携を確認する試験である。

このプロジェクトでは、ITb を自動テストには含めない。
理由は以下の通り。

- 外部 API の状態によりテスト結果が不安定になる。
- network error の影響を受ける。
- Google Maps API の quota、cost に影響する可能性がある。
- 自動テストの実行時間が長くなる。

ただし、必要に応じて以下を手動で確認する。

- Google Maps API key が正しく設定されていること。
- API key の制限が適切であること。
- 実際に地図表示できること。
- 実際にルート検索できること。
- API error 時に画面が壊れないこと。

### ST: シナリオテスト

ST は、複数機能をまたいだ業務シナリオ全体を確認する試験である。

このプロジェクトでは、ST を独立した試験工程としては実施しない。
必要なシナリオ確認は、UAT のチェックリストに含める。

### UAT: ユーザー受け入れテスト

UAT では、ユーザー目線で機能が受け入れ可能か確認する。

確認対象:

- 出発地、目的地、検索条件を入力できること。
- ルート情報が分かりやすく表示されること。
- カフェ情報が分かりやすく表示されること。
- Google Maps リンクを開けること。
- 入力不足時に分かりやすいエラーが表示されること。
- API error 時に分かりやすいエラーが表示されること。
- スマートフォン幅でも最低限利用できること。

## 実装後に実行する確認

実装完了前に以下を実行する。

```bash
npm run lint
npm run build
```

- `npm run typecheck` は script が存在する場合のみ実行する。
- `npm test` は script が存在する場合のみ実行する。
- docs のみの変更では確認コマンドを未実行にしてよい。
- 未実行理由は「docs のみの変更のため不要」とする。
- コマンドが存在しない場合、または実行できない場合は、その理由を説明する。
- 失敗した場合は、原因を確認して必要な修正を行う。
- 修正後は、失敗した確認コマンドを再実行する。

## テスト時の禁止事項

- UT で Google Maps API の実通信を行わない。
- ITa で外部 API の実レスポンスに依存しない。
- 外部 API の不安定さを理由に、アプリ内部のテスト失敗を放置しない。
- `any` や過剰な mock によって不具合を隠さない。
- typecheck、lint、test の失敗を無視しない。

## 完了時の出力

実装完了後は、以下を簡潔に示す。

- 実装内容
- 変更ファイル
- 実行した確認コマンド
- 確認結果
- 残っている注意点
