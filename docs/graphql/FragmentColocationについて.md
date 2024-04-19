# FragmentColocationについて

コンポーネントが必要とするデータを Fragment にまとめてコンポーネントと同じ場所に配置 (co-locate) すること。

## サンプルコード

**ViewerPage.tsx**

```tsx
import { useFetchViewerPageQuery } from './__generated__'

gql`
  query FetchViewerPage {
    viewer {
      id
      ...Profile_User
      posts {
        ...PostList_Post
      }
    }
  }
`

export const ViewerPage: FC = () => {
  const { data } = useFetchViewerPageQuery()

  if (!data) {
    return <p>Loading...</p>
  }

  const {
    viewer: { name, bio, posts },
  } = data

  return (
    <div>
      <Profile name={name} bio={bio} />
      <PostList posts={posts} />
    </div>
  )
}
```

**Profile.tsx**

```tsx
import { Profile_UserFragment } from './__generated__'

gql`
  fragment Profile_User on User {
    name
    bio
  }
`

type Props = Profile_UserFragment

export const Profile: FC<Props> = ({ name, bio }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{bio}</p>
    </div>
  )
}
```

**PostList**

```tsx
import { PostList_PostFragment } from './__generated__'

gql`
  fragment PostList_Post on Post {
    id
    date
    title
  }
`

type Props = {
  posts: PostList_PostFragment[]
}

export const PostList: FC<Props> = ({ posts }) => {
  return (
    <div>
      <h2>Posts</h2>
      <ul>
        {posts.map(({ id, date, title }) => {
          return (
            <li key={id}>
              {date}: {title}
            </li>
          )
        })}
      </ul>
    </div>
  )
}
```

## そもそも、GraphQLのfragmentsって何？

GraphQLにはフラグメントと呼ばれる再利用可能なユニットが含まれていて、フラグメントを使用すると、フィールドのセットを構築し、必要に応じてそれらをクエリに含めることができる。

### query

```graphql
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

# comparisonFieldsというフラグメントを定義
fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```

> 引用: https://graphql.org/learn/queries/#fragments

### response

```json
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

## Fragment Colocationのメリット

- 「Query や Mutation を実行するコンポーネント」と、「それらの結果を必要とするコンポーネント」との関心の分離ができること
- Fragment のフィールドを編集することで、 Query を直接編集することなく取得したいフィールドの更新ができること
- Fragment を元に生成した型を Props の型として使用することで、Props の保守性を高く保てること
- React Component と同じ場所に宣言することで、コンポーネントとクエリの依存関係が分かりやすくなること
- GraphQL Code Generator による型や hooks の生成ファイルを、colocate した単位で分割することで、巨大な単一の生成ファイルを読み込む場合と比べてバンドルサイズを減らすことができること

## 参考

- https://zenn.dev/moneyforward/articles/20221211-fragment-colocation
- https://github.com/Quramy/gql-study-workshop/tree/main/chapters
