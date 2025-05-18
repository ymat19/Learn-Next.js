# Next.js App Router Course - Starter

This is the starter template for the Next.js App Router Course. It contains the starting code for the dashboard application.

For more information, see the [course curriculum](https://nextjs.org/learn) on the Next.js Website.

## 備忘録

### chapter 1

#### プロジェクト作成

```cmd
npx create-next-app@latest <app name> --use-pnpm --ts
```

### chapter 2

#### Global CSS

ルートレイアウトでcssをimportする
(cssインポートがデフォルトで有効)

```tsx title="app/layout.tsx"
import "@/app/ui/global.css";
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

```tsx
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

#### コンポーネント

- クライアントコンポーネントとサーバサイドコンポーネントは別バンドル

##### クライアントコンポーネント

- 通常通りReact.createElementとして実行される
- サーバサイドコンポーネントをimportすることはできない
- サーバからFlightとして受け取って、jsxに復元されてブラウザで実行される
  - Flight: コンポーネントをjsonっぽくしたやつ
- Flightを解釈する処理はnextがクライアントのコードに注入していて、レンダリング前に解決される

##### サーバサイドコンポーネント

- asyncなコンポーネントも作れる
- サーバサイドで完結するものはあらかじめhtmlになり、クライアントコンポーネントが関わる部分はFlightにシリアライズされる
- サーバサイドコンポーネントがクライアントサイドコンポーネントをimportすることはできる
  - importするが実行はしない、flightで参照情報がクライアントにわたって実行される
- サーバサイドコンポーネントがクライアントサイドコンポーネントのchildrennにサーバサイドコンポーネントを埋め込むことはできる

```tsx
// これはいける
import ClientComponent from "./ClientComponent";
import ServerPart from "./ServerPart";

export default function Page() {
  return (
    <ClientComponent>
      <ServerPart />
    </ClientComponent>
  );
}
```

### chapter 9

#### loading.tsx

page.tsxがロードされるまで代わりに表示される
子のルートにも効く
子に効かせたくないならroute groupsを切る

#### route groups

パスを変えずに、同一のルートで内部的にディレクトリを切る
パスが変わらないので名前が重複するのはダメ

```txt
app/
  (auth)/
    login/
    register/
    users/
  (main)/
    home/
    profile/
    users/ ダメ
```

#### Suspens

page単位ではなくコンポーネント単位でストリーミングしたいならこれを使う

[Suspens](https://ja.react.dev/reference/react/Suspense)

#### fetchする場所

サーバサイドコンポーネントが自分自身でawait fetchして、そのコンポーネントをSuspenseでラップするのが推奨。分離性，再利用性が上がるため。

呼び出し元でfetchしてpropsで受けるのはサーバサイドコンポーネントでは非推奨。

サーバサイドコンポーネントは仮装DOMの制約を受けずasync, awaitできるのでfetchしても副作用にならない。(useEffectしなくて良いので狭義に純粋)

### chapter 11

#### useSearchParams

ReadonlyURLSearchParamsを返すので、下のようにコピーを作って書き換える

クライアントコンポーネントではこれを使う。クエリの変更がリアルタイムに反映される。

```tsx
const searchParams = useSearchParams();
const params = new URLSearchParams(searchParams);
```

#### useRouter

クライアントコンポーネントでこれを更新すると、Pageコンポーネントが再レンダリングされる。

サーバサイドコンポーネントはpropsの更新によって再実行されないので、サンプルではこの仕組みでクエリ更新時の再レンダリングをかけている。

```
  const { replace } = useRouter();
```

#### searchParams(props)

サーバサイドコンポーネントの場合、propsにクエリストリングのPromiseが注入される

SSR時点で値が確定する

#### 制御/非制御コンポーネント

inputのvalueをstateにバインドするものを制御コンポーネント、DOMに任せる場合非制御コンポーネントと呼ぶ

初期値を受けてユーザ入力に従うだけなら後者で十分、ロジックから値を操作するなら前者が望ましい

```tsx
// 制御
const [query, setQuery] = useState("");
return <input value={query} onChange={(e) => setQuery(e.target.value)} />;

// 非制御
<input
  defaultValue="Hoge"
  onChange={(e) => {
    doSomething(e.target.value);
  }}
/>;
```

#### useDebouncedCallback

npmパッケージで提供されるカスタムフック

コールバックの発火から実行まで指定の時間待機することで連続墓を防ぐ

#### ページング

ページングはクライアントでやるので、総ページ数は親のサーバサイドコンポーネントから渡しておく

### chapter 12

#### サーバアクション

クライアントに渡ったフォームがサーバ側の関数をRPC呼び出しできる

```
// Server Component
export default function Page() {
  // Action
  async function create(formData: FormData) {
    'use server';

    // Logic to mutate data...
  }

  // Invoke the action using the "action" attribute
  return <form action={create}>...</form>;
}
```

データ操作は以下で使い分ける

- データ取得に伴う再レンダリングを行うならURLを更新しサーバサイドコンポーネントを再レンダリングする
- 再レンダリングを伴わない場合(副作用)はサーバアクション

#### revalidatePath

キャッシュを消してページを再取得するよう要求する。全ユーザに影響する。

```tsx
revalidatePath("/dashboard/invoices");
```

サーバアクションでこれに続いてredirectもすればそのまま再レンダリングさせられる。

#### Dynamic Route Segments

`hoge/[id]/fuga` のようなルートを作れる

idはpropsに入る。サンプルは↓になってるが、最新のnextではPromiseじゃない？

```
export default async function Page(props: { params: Promise<{ id: string }> }) {
```

#### 引数付きでサーバアクションを呼ぶ

[bind](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)を用いて元の関数への参照を維持する


```
  // NG
  const updateInvoiceWithId = () => updateInvoice(null, invoice.id);
  // OK
  const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);
```

### chapter 13

#### error.tsx

例外がハンドリングされなかった場合、これがフォールバックUIとして表示される。

#### not-found.tsx

404の場合のエラーはerror.tsxとは別に用意できる。

```
import { notFound } from 'next/navigation';

export default async function Page(props: { params: Promise<{ id: string }> }) {
  const resource = query(id);
  if (!resource) {
    notFound();
  }
  return ...
}

```

### chapter 14

### chapter 15

### chapter 16
