# doko-cafe

## Project Overview（プロジェクト概要）

A Next.js application for finding cafes along a travel route.

Users input:
- origin: 出発地
- destination: 目的地
- transport mode: 移動手段（walking | driving | bicycle）

The application searches for cafes near the route.

---

## Tech Stack（技術構成）

- Next.js
- TypeScript
- Tailwind CSS
- Google Maps API
- DevContainer

---

## Directory Rules（ディレクトリ責務）

- app/: pages and routing
- components/: UI components
- services/: external API calls
- utils/: pure utility functions
- types/: TypeScript types
- constants/: constant values

---

## Coding Rules（コーディングルール）

- Use TypeScript
- Use functional components
- Separate UI logic and API logic
- Keep components small
- Avoid large files
- Avoid using any type
- Do not hardcode API keys
- Keep Google API logic inside services/
- Remove unnecessary console.log before commit

---

## Naming Rules（命名ルール）

- React components: PascalCase
- utility functions: camelCase
- constants: UPPER_SNAKE_CASE

---

## State Management（状態管理）

- Use React hooks
- Do not introduce Redux or Zustand for now

---

## Development Policy（開発方針）

Implement MVP first.

Phase1:
- walking only
- map display
- cafe list
- Google Maps link

---

## メンテナンス_ポリシー（Maintenance policy）

- 会話の中で繰り返し指示されたことがある場合は反映を検討すること
- 冗長だったり、圧縮の余地がある箇所を検討すること
- 簡潔でありながら密度の濃い文書にすること

---

## Commands（実行コマンド）

Start development server:

```bash
npm run dev