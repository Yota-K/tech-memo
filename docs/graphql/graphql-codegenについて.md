# graphql-codegenについて

## graphql-codegenについて

GraphQL Schemaを元に、クライアント側で使う型定義ファイルを生成してくれるライブラリ。

https://the-guild.dev/graphql/codegen

```yaml
# ファイルがすでに存在していた場合に上書きする。(デフォルトはtrue)
overwrite: true
# **必須のオプション。**スキーマファイルまたは、GraphQLのエンドポイントを指定する。
schema: "./generated/schema/external/**/*.gql"
# **必須オプション。**GraphQLスキーマを元に生成した型情報のファイルを出力する場所を指定する。
generates:
  app/javascript/graphql/generated.ts:
    # **必須オプション。**ファイルの生成時に使用するプラグインを指定する。
    plugins:
      - "typescript"
      - "typescript-operations"
      - "typescript-react-apollo"
```

## プラグインについて

プラグインは言語や用途に応じて色々ある。

- `@graphql-codegen/typescript`・・・GraphQLスキーマに基づいて、TypeScriptの型を生成する。
- `@graphql-codegen/typescript-resolvers`・・・リゾルバーの型定義を生成する。
- `@graphql-codegen/schema-ast`・・・複数のスキーマをマージして、1 つのスキーマとして出力する。

その他指定可能なオプションはこちらを参照。

https://the-guild.dev/graphql/codegen/docs/config-reference/codegen-config
