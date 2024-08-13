# 1. Heroku 시작하기

## 1-1. Heroku CLI 설치 및 로그인

### 1-1-1. Heroku CLI 설치

**macOS:**

```bash
brew tap heroku/brew && brew install heroku
```

<br>

**Windows:**

```bash
choco install heroku-cli
```

<br>

**Linux:**

```bash
curl https://cli-assets.heroku.com/install.sh | sh
```

<br><br>

### 1-1-2. Heroku CLI 로그인

```bash
heroku login
```

브라우저가 열리고 Heroku 계정에 로그인하면, CLI가 인증을 완료합니다.

<br><br><br>

## 1-2. 새로운 애플리케이션 생성

### 1-2-1. 프로젝트 디렉토리 생성

```bash
mkdir my-heroku-app
cd my-heroku-app
```

Git 리포지토리 초기화

```bash
git init
```

Heroku 애플리케이션 생성

```bash
heroku create my-heroku-app
```

이 명령어는 Heroku에 my-heroku-app이라는 이름으로 새로운 애플리케이션을 생성하고, 해당 애플리케이션의 Git 리모트를 추가합니다.

<br><br><br>

## 1-3. Node.js 애플리케이션 개발

### 1-3-1. Node.js 및 Express.js 설치

```bash
npm init -y
npm install express
```

<br><br>

### 1-3-2. 기본 서버 코드 작성

index.js 파일을 생성하고 다음 코드를 작성합니다:

```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello, Heroku!');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<br><br>

### 1-3-3. Procfile 생성

Heroku가 애플리케이션을 실행하기 위해서는 Procfile이 필요합니다. 이 파일을 생성하고 다음 내용을 작성합니다:

```plaintext
web: node index.js
```

<br><br><br>

## 1-4. 애플리케이션 배포

### 1-4-1. Git 커밋

```bash
git add .
git commit -m "Initial commit"
```

<br><br>

### 1-4-2. Heroku에 애플리케이션 배포

```bash
git push heroku master
```

배포가 완료되면, Heroku에서 애플리케이션을 실행하는 URL이 제공됩니다. URL을 통해 애플리케이션에 접근할 수 있습니다.

<br><br><br>

## 1-5. PostgreSQL 데이터베이스 연동

### 1-5-1. PostgreSQL 애드온 추가

Heroku에서 PostgreSQL 데이터베이스를 추가합니다:

```bash
heroku addons:create heroku-postgresql:hobby-dev
```

이 명령어는 무료 플랜의 Heroku Postgres 데이터베이스를 애플리케이션에 추가합니다.

<br><br>

### 1-5-2. 데이터베이스 URL 확인

```bash
heroku config:get DATABASE_URL
```

<br><br>

### 1-5-3. PostgreSQL 패키지 설치

Node.js 애플리케이션에서 PostgreSQL을 사용하기 위해 pg 패키지를 설치합니다:

```bash
npm install pg
```

<br><br>

### 1-5-4. 데이터베이스 연동 코드 작성

index.js 파일을 수정하여 PostgreSQL과의 연결을 추가합니다:

```javascript
const express = require('express');
const { Client } = require('pg');
const app = express();
const PORT = process.env.PORT || 3000;

const client = new Client({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }
});

client.connect();

app.get('/', async (req, res) => {
  try {
    const result = await client.query('SELECT NOW()');
    res.send(`Database time: ${result.rows[0].now}`);
  } catch (err) {
    console.error(err);
    res.send('Error connecting to the database');
  }
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

<br><br>

### 1-5-5. 변경 사항 배포

```bash
git add .
git commit -m "Add PostgreSQL integration"
git push heroku master
```

<br><br><br>

## 1-6. 서버리스 함수 (간단한 API 엔드포인트)

Heroku에서는 서버리스 함수와 유사한 API 엔드포인트를 구현할 수 있습니다.

<br>

### 1-6-1. 서버리스 함수 코드 추가

index.js 파일에 새로운 엔드포인트를 추가합니다:

```javascript
app.get('/function', (req, res) => {
  const result = { message: 'This is a serverless function response' };
  res.json(result);
});
```

<br><br>

### 1-6-2. 변경 사항 배포

```bash
git add .
git commit -m "Add serverless function route"
git push heroku master
```

<br><br>

## 1-7. 프로젝트 파일 트리 구조

프로젝트의 파일 트리 구조는 다음과 같습니다:

```go
my-heroku-app/
├── index.js
├── node_modules/
├── package.json
├── Procfile
├── .gitignore
└── .git/
```

<br><br>

### 1-7-1. .gitignore 파일

프로젝트 루트에 .gitignore 파일을 생성하여 다음 내용을 추가합니다:

```plaintext
node_modules
.env
```

<br><br><br>

## 1-8. 애플리케이션 테스트

애플리케이션이 배포된 후, 웹 브라우저를 통해 애플리케이션을 테스트할 수 있습니다. Heroku에서 제공하는 URL을 통해 애플리케이션의 루트 엔드포인트 및 /function 엔드포인트를 확인합니다.

<br>

### 1-8-1. 실시간 로그 확인

Heroku의 실시간 로그를 확인하려면 다음 명령어를 사용합니다.

```bash
heroku logs --tail
```

이 명령어를 통해 애플리케이션의 로그를 실시간으로 확인할 수 있습니다.

<br><br><br>

## 1-9. 애플리케이션 관리 및 모니터링

Heroku 대시보드에서 애플리케이션의 성능, 로그, 환경 변수 등을 모니터링할 수 있습니다. Heroku 대시보드에 로그인하여 애플리케이션을 관리하고 설정을 조정할 수 있습니다.