# Instaclone

Instaclone Backend.

```command
npm init -y
```

## 설치한 확장기능

- GraphQL
- Prisma

## 3.1 Apollo Server

- GraphQL 서버 구축

```command
npm i apollo-server@2.19.1 graphql
npm i nodemon -D
```

- server.js 파일 작성

## 3.2 Babel

- 바벨을 통해서 최신 문법 import 사용

```command
npm i -D @babel/core @babel/preset-env @babel/node
```

- babel.config.json 파일 작성

```json
{
  "presets": ["@babel/preset-env"]
}
```

- package.json 파일 작성

```json
{
  "scripts": {
    "dev": "nodemon --exec babel-node server"
  }
}
```

- 바벨이 모든 노드 버전들에서 이해 가능한 옛날 자바스크립트 문법으로 컴파일해준다.

## 3.4 Prisma Setup

- SQL 코드를 쓸 필요 없이 자바스크립트 코드를 작성하면 Prisma가 데이터베이스와 대신 말해준다.

```command
npm i prisma -D
npx prisma init
```

- [postgresql 다운로드](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)
- [pgAdmin4 다운로드](https://www.pgadmin.org/download/pgadmin-4-windows)

- pgAdmin 4 설치 이후 프로그램 실행해서 instaclone 데이터베이스를 만든다.(Owner: postgres)
- .env 파일에 아래와 같이 수정

```env
DATABASE_URL="postgresql://postgres:qwer1234@localhost:5432/instaclone?schema=public"
```

## 3.5 Prisma Migrate

- prisma prettier 적용을 위한 설정

```js
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": null,
  // "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

```command
npx prisma migrate dev
```

- [Developing with Prisma Migrate](https://www.prisma.io/docs/guides/database/developing-with-prisma-migrate)

## 3.6 Prisma Client

- Type Definitions가 Prisma와 GraphQL로 작업할 때 type definitions와 schema를 일치시켜야 한다.

- server.js

```js
const typeDefs = gql`
  type Movie {
    id: Int!
    title: String!
    year: Int!
    genre: String
    createdAt: String!
    updatedAt: String!
  }
`;
```

- schema.prisma

```prisma
model Movie {
  id        Int      @id @default(autoincrement())
  title     String
  year      Int
  genre     String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

## 3.7 Prisma Client part Two

```js
const client = new PrismaClient();

const typeDefs = gql`
  type Movie {
    id: Int!
    title: String!
    year: Int!
    genre: String
    createdAt: String!
    updatedAt: String!
  }
  type Query {
    movies: [Movie]
    movie(id: Int!): Movie
  }
  type Mutation {
    createMovie(title: String!, year: Int!, genre: String): Movie
    deleteMovie(id: Int!): Movie
    updateMovie(id: Int!, year: Int!): Movie
  }
`;

const resolvers = {
  Query: {
    movies: () => client.movie.findMany(),
    movie: (_, { id }) => client.movie.findUnique({ where: { id } }),
  },
  Mutation: {
    createMovie: (_, { title, year, genre }) =>
      client.movie.create({
        data: {
          title,
          year,
          genre,
        },
      }),
    deleteMovie: (_, { id }) => client.movie.delete({ where: { id } }),
    updateMovie: (_, { id, year }) =>
      client.movie.update({ where: { id }, data: { year } }),
  },
};

const server = new ApolloServer({
  typeDefs,
  resolvers,
});

server
  .listen()
  .then(() => console.log("Server is running on http://localhost:4000"));
```

- prisma client를 통해 CRUD

- Playground에서는 다음과 같이 실행

```graphql
mutation {
  createMovie(title: "Nick is back", year: 2000) {
    id
  }
}

{
  movie(id: 2) {
    title
    year
    updatedAt
    createdAt
  }
}

mutation {
  updateMovie(id: 2, year: 1967) {
    id
  }
}
```

## 3.8 Prisma Studio

```command
npx prisma studio
```

## 3.10 Architecture part Two

```command
npm i graphql-tools@7.0.2
```

- [GraphQL Tools > Doc > Schema merging](https://www.graphql-tools.com/docs/schema-merging#file-loading)

## 참고 링크

- [강좌 저장소](https://github.com/nomadcoders/instaclone-backend)
- [듣던 강좌 #3.11](https://nomadcoders.co/instaclone/lectures/2402)
