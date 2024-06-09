# nextjsをフルスタックFWとして使う際の覚書

## Server Actionsを導入することによって起こり得るセキュリティ的リスクに関して

- 発生しうる問題
    - フロントとバックエンドの境界が曖昧になることによって、クライアントサイドに渡るとまずい情報が渡ってしまうリスクがある
    - 渡るとまずい情報
        - DBの接続情報
        - API Key
        - 個人情報

### 防止策

#### Server Actionsを導入して、Server Actionsとして扱う関数は特定のディレクトリに集約する

特定のディレクトリに集約した上で、ESLintでServer Actionsとして扱う関数を集約したディレクトリからしかimportできない設定を行う。

クライアントサイドからServer Actionsとして扱う関数をimportすることができなくなり、セキュリティリスクを軽減することができる。

- 実現可能なESLintのプラグイン
    - no-restricted-imports: https://eslint.org/docs/latest/rules/no-restricted-imports
    - no-restricted-paths: https://github.com/import-js/eslint-plugin-import/blob/main/docs/rules/no-restricted-paths.md

```
.
└── actions: Server Actionsとして扱う関数を集約するディレクトリ
   ├── createPost.ts
   └── updatePost.ts
```

#### server-onlyディレクティブの活用

あとでかく

## 設計周り

- nextjsをフルスタックFWとして使う際の設計で難しい部分
- フロントとバックエンドで主流になっているパラダイムが異なるため、その違いをどう吸収するかがむずい
   - フロント: 関数型プログラミング
   - バックエンド: オブジェクト指向プログラミング(OOP)

いっそバックエンドはOOPに寄せてしまい、フロントからは実装したクラスをnewして使うみたいにした方がいい感じの設計になりそう。

**最初構成（イメージ）**

アプリの規模感に応じて、entity classとかもあるといいかも。

```
.
├── components
│  ├── Hoge.tsx
│  └── Fuga.tsx
├── usecase: ユースケース層
├── service: サービス層
├── repository: リポジトリ層
└── actions: Server Actionsとして扱う関数を集約するディレクトリ
   ├── createPost.ts
   └── updatePost.ts
```

この設計方針のメリットとしては、フロント・バックエンドが分業になっているプロジェクトでも、作業の分担がしやすい。（Server Actionsとして実行可能な関数の実装で1つのタスクとして振れる）

### データの更新

```ts
"use server";

export const updatePost = async (postId: string) => {
  const post = new Post();
  await post.update(postId);
  return post;
}
```

### データの取得

```tsx
const getPosts = async () => {
  const post = new Post();
  const posts = await post.getPosts();
  return posts;
}

const Page = async () => {
  const posts = await getPosts();

  return (
    <div>
      {posts.map((post) => (
        <div key={post.id}>{post.title}</div>
      )}
    </div>
  )
}
```
