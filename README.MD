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

```json
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

## 참고 링크

- [강좌 저장소](https://github.com/nomadcoders/instaclone-backend)
- [듣던 강좌](https://nomadcoders.co/instaclone/lectures/2401)
