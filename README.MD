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

## 3.11 Dotenv

```command
npm i dotenv
```

```env
PORT=4000
```

```js
require("dotenv").config();

const PORT = process.env.PORT;
server.listen(PORT);
```

## 3.12 Recap

- prisma 폴더 삭제 => movies 폴더 삭제 => instaclone 데이터베이스 DROP => 새로운 instaclone 데이터베이스 생성

## 4.0 Create Account part One

```command
npx prisma init
```

- prisma 초기화

```prisma
model User {
  id        Int      @id @default(autoincrement())
  firstName String
  lastName  String?
  username  String   @unique
  email     String   @unique
  password  String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

- schema.prisma에 User 모델 작성

```command
npm run migrate
```

- 마이그레이션 이름 => user_model

```js (users.typeDefs.js)
export default gql`
  type User {
    id: String!
    firstName: String!
    lastName: String
    username: String!
    email: String!
    createdAt: String!
    updatedAt: String!
  }
`;
```

- prisma schema를 graphQL schema랑 동기화

- schema를 수정했기에 다시 마이그레이션

```command
npm run migrate
```

- 마이그레이션 이름 => createdAt_updatedAt

- \*prisma schema를 변경할 때마다 migrate를 해줘야한다.

- Prisma schema, graphQL schema 동기화 했으니 mutations, queries 타입도 정의해준다.

```js (users.typeDefs.js)
export default gql`
  type User {
    id: String!
    firstName: String!
    lastName: String
    username: String!
    email: String!
    createdAt: String!
    updatedAt: String!
  }
  type Mutation {
    createAccount(
      firstName: String!
      lastName: String
      username: String!
      email: String!
      password: String!
    ): User
  }
  type Query {
    seeProfile(username: String): User
  }
`;
```

## 4.1 Create Account part Two

- [Prisma Client > Filtering](https://www.prisma.io/docs/concepts/components/prisma-client/filtering-and-sorting#filter-conditions-and-operators)

## 4.2 Create Account part Three

```command
npm i bcrypt
```

```graphql
mutation {
  createAccount(
    firstName: "Nico"
    lastName: "las"
    email: "nico@las.com"
    password: "123"
    username: "nico"
  ) {
    username
  }
}
```

## 4.4 login

```command
npm i jsonwebtoken
```

- token은 서버가 프론트엔드에 연결되어 있지 않거나 따로 떨어진 경우 사용한다.
- cookie와 session은 서버와 프론트엔드가 같은 곳에 있을 때 사용하기 적합하다.

## 4.5 login part Two and Refactor

- 토큰의 목적은 토큰 안에 정보를 넣고, 그 토큰이 우리가 싸인했던 토큰인지 확인하는 것이다.
- 여기엔 비밀 정보를 넣으면 안된다.
- 토큰의 다른 목적은 다른 사람이 변경하지 못하게 하는 것이다.

## 4.14 File Upload part One

- User 모델에 bio, avatar를 추가하고 마이그레이션한다.
- 기존 graphql-tools를 사용하여 생성하던 스키마를 apollo server를 이용해 스키마를 생성할 수 있도록 한다. 그렇게해야 Upload 타입을 사용할 수 있다.

## 4.15 File Upload part Two

- playground는 file upload를 할 수 없다. 그래서 graphQL 클라이언트를 사용한다.
- [Altair](https://altair.sirmuel.design/#download)
- chrome 확장 프로그램으로 설치해서 실행

## 4.18 Ejecting from Apollo Server

```command
npm i express apollo-server-express@2.19
```

- Apollo Server는 할 수 있는게 제한적이다.
- Rest API를 쓰는 경우 Express Server가 필요하다.

```command
npm i morgan
```

## 브랜치

- BACKEND-SETUP : 3강까지 소스 (movies 테스트)

## 참고 링크

- [강좌 저장소](https://github.com/nomadcoders/instaclone-backend)
- [듣던 강좌 #4.20](https://nomadcoders.co/instaclone/lectures/2414)
