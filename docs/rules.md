# 開発ルール

このファイルは、doko-cafe の詳細なコーディング規約と命名規約を定義する。
実装時は `AGENTS.md` とこのファイルの両方に従う。

---

## Coding Rules（コーディングルール）

### 1. 基本方針

- 読みやすく、保守しやすいコードを優先する。
- 複雑で賢そうに見えるコードより、意図が明確なコードを優先する。
- UI Component に業務ロジックや外部 API 呼び出しを直接書かない。
- 責務ごとにディレクトリ、ファイル、関数を分離する。
- 新しいライブラリを追加する場合は、追加理由を説明する。
- 大きすぎる Component は分割する。
- export する関数、service 関数、外部 API レスポンスを扱う関数には戻り値の型を明示する。

### 2. Next.js のルール

- Next.js の App Router を前提とする。
- 原則として Server Component を使用する。
- Client Component は、必要な場合にのみ使用する。
- `useState`、`useEffect`、event handler、ブラウザ API、地図操作などが必要な場合のみ `use client` を付与する。
- page-level component に安易に `use client` を付与しない。
- Client Component はできるだけ小さく保つ。
- データ取得は、可能な限り Server Component または service 層で行う。
- ブラウザ依存の処理は、Client Component または custom hook に分離する。
- route segment は kebab-case で命名する。
- route 固有の Component は、該当 route の近くに配置する。
- 複数画面で再利用する Component は `components/` に配置する。

例:

```text
app/
  cafe-search/
    page.tsx
  route-search/
    page.tsx
components/
features/
services/
utils/
types/
constants/
config/
```

### 3. TypeScript のルール

- `any` は原則として使用しない。
- 型が不明な場合は `unknown` を使用し、利用前に型を絞り込む。
- `as any` による型エラーの抑制は避ける。
- やむを得ず型アサーションを使う場合は、理由をコメントで説明する。
- 外部 API レスポンスには専用の型を定義する。
- export する関数には戻り値の型を明示する。
- feature 固有の型は feature 内に配置する。
- 複数箇所で使う共通型は `types/` に配置する。
- 原則として `type` を使用する。
- 拡張可能な契約を表す場合のみ `interface` を検討する。
- `interface` 名に `I` prefix は付けない。

良い例:

```ts
type CafeSearchResult = {
  id: string;
  name: string;
  address: string;
  rating?: number;
};

type RouteSearchParams = {
  origin: string;
  destination: string;
};
```

悪い例:

```ts
interface ICafe {}

const data: any = {};
```

### 4. React / Component のルール

- Component 名は PascalCase にする。
- Component ファイル名は PascalCase にする。
- 1 ファイルにつき、原則として 1 つの主要 Component を export する。
- Component は表示責務を中心にする。
- 複雑なロジックは custom hook、service、utils に分離する。
- presentational component では API 呼び出しを行わない。
- props の型名は `{ComponentName}Props` とする。
- props は必要最小限にする。
- Google Maps API の生レスポンスを Component に直接渡さない。
- 外部 API レスポンスは、service 層でアプリ内部用の型に変換してから Component に渡す。

例:

```text
components/
  CafeCard.tsx
  RouteSummary.tsx
  GoogleMapView.tsx
```

良い例:

```tsx
type CafeCardProps = {
  cafe: CafeSearchResult;
};

export function CafeCard({ cafe }: CafeCardProps) {
  return <article>{cafe.name}</article>;
}
```

### 5. custom hook のルール

- custom hook 名は必ず `use` で始める。
- hook 名は、何を扱う hook なのか分かる名前にする。
- 1 つの hook に複数の無関係な責務を持たせない。
- 再利用するブラウザ API 処理は custom hook に分離する。

良い例:

```ts
useCurrentLocation();
useCafeSearch();
useRouteSearch();
```

悪い例:

```ts
getCurrentLocationHook();
useData();
useCommon();
```

### 6. service のルール

- 外部 API アクセスは `services/` に配置する。
- Google Maps API へのアクセスは `services/googleMaps/` に配置する。
- service 関数は、原則として外部 API の生レスポンスをそのまま返さない。
- service 関数では、外部 API レスポンスをアプリ内部用の型に変換する。
- service 関数には戻り値の型を明示する。
- service ファイル名は、実行する処理が分かる動詞から始める。

例:

```text
services/
  googleMaps/
    searchCafes.ts
    searchRoute.ts
    buildGoogleMapsUrl.ts
```

良い例:

```ts
export async function searchCafes(
  params: CafeSearchParams
): Promise<CafeSearchResult[]> {
  // implementation
}
```

### 7. utils のルール

- `utils/` には純粋関数を配置する。
- utility function は React に依存させない。
- utility function では外部 API を呼び出さない。
- utility function 名は、変換・計算・生成内容が分かる名前にする。

良い例:

```ts
formatDistance();
formatDuration();
calculateCenterPoint();
```

悪い例:

```ts
handleData();
process();
common();
```

### 8. Tailwind CSS のルール

- スタイリングは Tailwind CSS の utility class を優先する。
- 動的な値が必要な場合を除き、inline style は避ける。
- 繰り返し出現する UI パターンは Component として切り出す。
- `className` が長くなりすぎる場合は、Component 分割を検討する。
- Tailwind CSS で表現できる内容に対して、不要な custom CSS を作成しない。
- responsive prefix である `sm:`、`md:`、`lg:` は意図を持って使用する。
- layout 用 Component を過剰にネストしない。

### 9. Google Maps API のルール

- Google Maps API key をコードに直接書かない。
- API key は環境変数で管理する。
- Client 側に公開される API key は、Google Cloud 側で適切に制限する。
- Google Maps API 呼び出しは `services/googleMaps/` に集約する。
- route、cafe、location、map link にはアプリ内部用の型を定義する。
- UI Component が Google Maps API の生レスポンス構造に依存しないようにする。
- quota error、API error、network error は明示的に処理する。
- Google Maps リンク生成は `services/googleMaps/` の専用関数に分離する。

例:

```ts
buildGoogleMapsRouteUrl();
buildGoogleMapsPlaceUrl();
```

### 10. エラーハンドリングのルール

- error を握りつぶさない。
- UI にはユーザー向けの分かりやすいエラーメッセージを返す。
- 技術的な詳細は log に残し、UI に直接表示しない。
- 外部 API 呼び出しでは try/catch を使用する。
- 外部 API の error object をそのまま Component に投げない。

---

## Naming Rules（命名規約）

### 1. ファイル・ディレクトリ名

```text
Component ファイル: PascalCase
  CafeCard.tsx
  RouteSummary.tsx

custom hook ファイル: use で始まる camelCase
  useCafeSearch.ts
  useCurrentLocation.ts

service ファイル: 動詞で始まる camelCase
  searchCafes.ts
  searchRoute.ts
  buildGoogleMapsUrl.ts

utility ファイル: camelCase
  formatDistance.ts
  calculateCenterPoint.ts

route directory: kebab-case
  cafe-search
  route-search
```

### 2. 変数名

- 変数名は camelCase にする。
- boolean の変数名は、意味に応じて `is`、`has`、`can`、`should` から始める。
- `data` のような曖昧な名前は、文脈が明確な短い範囲に限定する。

良い例:

```ts
const isLoading = true;
const hasError = false;
const canSearch = origin !== "" && destination !== "";
const shouldShowMap = cafes.length > 0;
```

悪い例:

```ts
const loadingFlag = true;
const errorFlg = false;
const data = [];
```

### 3. 関数名

- 関数名は動詞から始める。
- 何を取得・変換・生成・判定する関数なのか分かる名前にする。
- `doSomething`、`handleStuff`、`processData` のような曖昧な名前は避ける。

良い例:

```ts
searchCafes();
searchRoute();
formatDistance();
buildGoogleMapsRouteUrl();
calculateCenterPoint();
```

悪い例:

```ts
doSearch();
handleStuff();
processData();
getData();
```

### 4. 型名

- 型名は PascalCase にする。
- 型名にはドメイン上の意味を含める。
- `Data`、`Result`、`Info` のような曖昧な型名は避ける。

良い例:

```ts
type Cafe = {};
type CafeSearchResult = {};
type RouteSearchParams = {};
type RouteSummary = {};
type LocationPoint = {};
```

悪い例:

```ts
type Data = {};
type Result = {};
type Info = {};
```

### 5. 定数名

- アプリ全体で使う固定値は UPPER_SNAKE_CASE にする。
- 関数内の local constant は camelCase にする。

良い例:

```ts
const DEFAULT_SEARCH_RADIUS_METERS = 1000;
const GOOGLE_MAPS_BASE_URL = "https://www.google.com/maps";
```

---

## AI への作業指示

- 作業前に `AGENTS.md` と `docs/rules.md` を読む。
- 既存のディレクトリ構成に従う。
- 新しい設計方針や architectural pattern を導入する場合は、理由を説明する。
- 不要な抽象化を追加しない。
- 変更は小さく、責務を明確にする。
- 実装後に、変更したファイルと変更理由を説明する。
- 実装完了前に `npm run lint` と `npm run build` を実行する。
- `npm run typecheck` は script が存在する場合のみ実行する。
- `npm test` は script が存在する場合のみ実行する。
- docs のみの変更では確認コマンドを未実行にしてよい。
- 未実行理由は「docs のみの変更のため不要」とする。
- 判断に迷う場合は、単純な実装を優先し、trade-off を説明する。
