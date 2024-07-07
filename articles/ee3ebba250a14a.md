---
title: "Prisma Multi-file"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Prisma","typeORM","DB","TypeScript"]
published: true
---

### Split your Schema
Prisma v5.15.0でSchemaファイルの分割ができるようになりました🎉

**機能が追加された理由**
これまではPrismaは、1つのスキーマファイルしかサポートしていませんでした...
えっ！今までできなかったの？そうです！今までは、1つのスキーマファイルにDBのモデルを集約していました。

**今回**
Prismaスキーマファイルを複数のファイルで使用できるようになり、データベーススキーマの管理性と構成性が向上し、大規模なプロジェクトでの作業が容易になる。
全てのモデルは全てのファイルを参照することができるので、リレーションはファイルを跨ぐことができます。

**使い方**
1. prismaSchemaFolderPreviewを有効化
```jsx
generator client {
  provider        = "prisma-client-js"
  previewFeatures = ["prismaSchemaFolder"] // <= previeFeaturesを追加
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL") 
}
```

2. 複数のPrismaのスキーマファイルを使用するには、prismaディレクトリ内にschemaディレクトリを追加
スキーマ・ディレクトリに追加ファイルを作成できるようになった。
ディレクトリ構造(リバーシの場合)
```
├── prisma
    └── schema   // <= schemaディレクトリの作成
        ├── games.prisma
        ├── moves.prisma
        ├── schema.prisma
        ├── squares.prisma
        └── turns.prisma
```

game.prisma
```jsx
model games {
    id          Int            @id @default(autoincrement())
    started_at  DateTime
    turns       turns[]
    game_result game_results[]
}

model game_results {
    id          Int      @id @default(autoincrement())
    game_id     Int
    winner_disc Int
    end_at      DateTime
    games       games    @relation(fields: [game_id], references: [id])
}
```

turns.prisma
```jsx
model turns {
    id         Int      @id @default(autoincrement())
    game_id    Int
    turn_count Int
    next_dixc  Int
    end_at     DateTime
    games      games    @relation(fields: [game_id], references: [id])
    moves      moves[]

    @@unique([game_id, turn_count])
}
```

**複数ファイルのユースケース**
プロジェクトが大きくなるにつれて、単一ファイルのPrismaスキーマでは最終的にサイズが大きくなり、効率的に管理ができなくなるという話を見聞きすることがあります。

この機能は、次のような場合に役に立ちます。
- スキーマが複雑な場合
    - スキーマに大きなモデルや複雑なリレーションがある場合、関連するモデルを別のファイルにすることで、スキーマの理解とナビゲートが容易になります
- 大規模なチーム
    - 1つのPrismaスキーマファイルがあり、多くの開発者がコミットしている場合、日中にかなり厄介なマージ競合に遭遇する可能性があります。大きなスキーマを複数のファイルに分割することで、このような苦痛を減らすことができます

**複数ファイルのPrismaスキーマ Tips**
この機能を最大化するためにいくつかのパターンがあります
- ドメインごとにファイルを整理
    - 関連するモデルごとにファイルにまとめます。ユーザ関連のモデルはuser.prisma,Post関連のモデルはpost.prismaに置く
- 明確な命名規則を使用する
    - スキーマファイル名は明確かつ簡潔でなければなりません。「myModels.prisma」や「CommentFeatureSchema.prisma」ではなく、user.prismaやpost.prismaのような名前を使用してください
- ビルトインのスキーマファイル
    - スキーマファイルはいくつあっても構いませんが、データソースとジェネレーターのブロックを定義する場所が必要です。「main.prisma」,「schema.prisma」,「base.prisma」はうまく機能するスキーマファイルです

参考文献
https://www.prisma.io/docs/orm/prisma-schema/overview/location#multi-file-prisma-schema
https://www.prisma.io/blog/organize-your-prisma-schema-with-multi-file-support