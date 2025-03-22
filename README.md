# Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## 備忘録

### chapter 1

#### プロジェクト作成

``` cmd
npx create-next-app@latest <app name> --use-pnpm --ts
```

### chapter 2

#### Global CSS

ルートレイアウトでcssをimportする
(cssインポートがデフォルトで有効)

``` tsx title="app/layout.tsx"
import '@/app/ui/global.css';
```

エイリアスは↓

```json title="tsconfig.json"
    "paths": {
      "@/*": ["./*"]
    }
```

#### CSS Modules

<https://nextjs.org/docs/app/getting-started/css#css-modules>

これも最初から使える

`<module name>.module.css`をインポートして`className`に設定すると、ユニークなクラス名が定義されてそのコンポーネントでだけスタイルが効く

#### clsx

<https://github.com/lukeed/clsx>
クラス名間のスペース忘れを防止して、条件分岐がきれいに書ける？

create-next-appの備え付けではない

### chapter 3

フォントはルートレイアウトのbodyに設定して全体に効かせる

`next/font/google`からフォントをインポートして適用すると、ビルド時にフォントが組み込まれる？

### chapter 4

#### `layout.tsx`

Layoutコンポーネントをexportする必要がある

childrenには↓が入ってくる

- 自身のルートのpage.tsx
- 子のルートのlayout.tsx

``` tsx
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div>
      Layout Div
      <div>{children}</div>
    </div>
  );
```

### chapter 5

#### Linkコンポーネント

Nextにレイアウトを部分レンダリングさせるには`Link`コンポーネントを使う  
(親Layoutを読み込むと子レイアウトがprefetchされるらしい)

#### usePathname

Nextで現在のパスを扱うためのカスタムフック  
next組み込みrouterでパス遷移すると更新される

### chapter 6

PostgreSQLが先月消えたっぽくて選べない
[回避策](https://github.com/vercel/next-learn/issues/951)

[Tagged templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates)

```ts
sql`SELECT * FROM users WHERE id = ${userId}`;
↓
sql(
  ["SELECT * FROM users WHERE id = ", ""],
  userId
);
```

### chapter 7

### chapter 8

### chapter 9

### chapter 10

### chapter 11

### chapter 12

### chapter 13

### chapter 14

### chapter 15

### chapter 16
