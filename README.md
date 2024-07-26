# Questions:

## Hãy mô tả cách bạn sẽ mở rộng và tối ưu hoá hiệu suất cho ứng dụng của mình trong tương lai.

**Về phần mở rộng:**

***Source code:***
- Hiện tại source này được dựng dựa trên monolithic, chỉ thích hợp cho các ứng dụng tầm trung trở xuống.
- Để có thể mở rộng source sau này, chúng ta có thể chuyển đổi source thành monorepo - NestJS đã support sẵn.
- Với Monorepo ta có thể chia nhỏ các module thành các app service riêng, hỗ trợ cho việc áp dụng mô hình microservice một cách dễ dàng. Đồng thời chia nhỏ ứng dụng, dễ quản lý, hạn chế việc crash đồng loạt.

***Chức năng:***
- Ứng dụng hiện tại chỉ là một ứng dụng quản lý task của người dùng nhỏ.
- Ta có thể tham khảo các ứng dụng quản lý task khác như Jira hoặc Notion. Từ đó ta có thêm các ý tưởng để mở rộng hoặc thực hiện những chức năng cần thiết ( vd: Thêm Tag cho Task, ngày bắt đầu, ngày kết thúc, assign,...).

**Về phần hiệu suất:**
- Hệu suất ta có thể tối ưu ở rất nhiều bước, bao gồm cả FE, BE và cơ sở hạ tầng
- Đối với BE, các bước tối ưu có thể bao gồm:
  - `Code`: Điều đầu tiên chính là phải nhìn vào code, ta có thể refactor lại code để cải thiện tốc độ API. Nếu code có thể đã tối ưu gần như hết mức trong khả năng thì mới nhìn đến các bước tiếp theo.
  - `Caching`: Dựa trên nhu cầu sử dụng của chức năng, nếu lượng request quá lớn thì ta có thể cache lại những dữ liêu thường xuyên lặp lại để phần nào đó giảm tải xuống database. Việc sử dụng loại cache nào thì còn tuỳ vào chức năng và cache có nhiều tầng để áp dụng ( vd: Redis, Memcache, Database ).
  - `Database`: Về database, lượng dữ liệu quá lớn có thể sẽ ảnh hưởng tới tốc độ query. Để khắc phục điều đó ta có thể áp dụng Index, Partition, View nhằm tối ưu tốc độ query ( mặc dù có thể đánh đổi một ít tốc độ ghi )
  - `Instance`: Khi áp dụng mô hình microservice, ta có thể scale instance để tránh tình trạng quá tải lượng request có thể nhận của một instance ( dựa trên framework, cơ sở hạ tầng để đánh giá).

## Làm thế nào để bạn đảm bảo rằng ứng dụng của bạn là an toàn và bảo mật thông tin người dùng?

**Về phần bảo mật:**

- Em đang áp dụng JWT cho source này theo cơ chế ( Refresh Token Rotation ).
- Tuy vẫn có một ít khả năng nào đó người dùng bị lộ `AccessToken`, nhưng với việc điều chỉnh `ExpireTime` ta có thể hạn chế được thời gian hành động của người chiếm dụng và người dùng .
- Hoặc ta có thể tham khảo một số cơ chế bảo mật khác (vd: HttpCookie, Device Session).

**Về phần client:**

- Áp dụng RateLimit tránh DDos
- CORS

## Giải thích cách bạn sẽ triển khai ứng dụng của mình lên một máy chủ.
- Trong trường hợp có sẵn máy chủ: `host`:`port`
- Thường thì em dùng GithubAction hoặc Jenkins để build và chạy script trên server.
- Các bước thực hiện:
  - Viết workflows đối với GithubAction và Jenkinfile đối với Jenkins
  - Set env cho nền tảng bao gồm `host`, `port`, `identity`
  - Setup webhook (Jenkins)
- Ví dụ: Trigger 1 lệnh build => Kiểm tra => Build => Push Dockerhub => Chạy script trên server ( docker compose ) => Clean


# [Project name] MCUD Assessment

- A simple application for managing user task

# Prerequisites

Before you continue, ensure you meet the following requirements:
- Node: version >= 18.20.4
- MikroCLI: version ^6.3.0
- Docker

## Installation

```shell
yarn install
```

## Running

Setup

### Env

Setup Env
```env
...
PORT =
ENVIRONMENT =

INIT_USERNAME =
INIT_PWD =

SECRET_KEY =
RF_SECRET_KEY =
JWT_SECRET_EXPIRE_PERIOD =
JWT_REFRESH_SECRET_EXPIRE_PERIOD =
...
```

### Database

This app is using PostgreSQL for database

#### Database on local machine

- Setup env for database config ( .env file )
```env
DB_HOST =
DB_PORT =
DB_USER =
DB_PWD =
DB_NAME =
```

#### Database with docker

- Setup env for database config ( .env file )
```env
DB_HOST =
DB_PORT =
DB_USER =
DB_PWD =
DB_NAME =
```

- Run docker compose database
- Make sure you are at project root folder

```shell
pwd # .../mcud-assessment

# run database
docker compose -f docker-compose.database.yml up -d
```

There are 2 approach to run this app:
- Run Local
- Run with docker

### Run Local

- Run migration command
```shell
pwd # .../mcud-assessment

# run migration
npx mikro-orm migration:up --config ./src/core/database/orm.config.ts
```

- Run app
```shell
yarn start
```

### Run with docker

- Build app docker image
```shell
docker build -t mcud-app .
```

- Run docker compose command
```shell
docker compose -f docker-compose.app.yml up -d
```

## Features

#### Swagger: `http://{host}:{port}/api`

### User

**Register**

- **Route:** `api/auth/register`
- **Method:** `POST`
- **JWT:** `none`
- **Body:**
```ts
username: string; // required
password: string; // required
```
- **Response:**
```ts
accessToken: string;
refreshToken: string;
accessExpiredAt: number;
refreshExpiredAt: number;
accessExpiredDate: Date;
refreshExpiredDate: Date;
```

**Login**

- **Route:** `api/auth/login`
- **Method:** `POST`
- **JWT:** `none`
- **Body:**
```ts
username: string; // required
password: string; // required
```
- **Response:**
```ts
accessToken: string;
refreshToken: string;
accessExpiredAt: number;
refreshExpiredAt: number;
accessExpiredDate: Date;
refreshExpiredDate: Date;
```

**Update password**

- **Route:** `api/user/update/password`
- **Method:** `POST`
- **JWT:** `mandatory`
- **Body:**
```ts
newPassword: string; // required
```
- **Response:**
```ts
boolean
```

### Task

**Get List**
- **Route:** `api/task/list`
- **Method:** `POST`
- **JWT:** `mandatory`
- **Body:**
```ts
queryFields: {
  title: string       // optional
  status: ETaskStatus // optional, enum: DONE|NOT_YET
  deadline: Date      // optional
};
orderFields: {
  createdAt: Sort;    // optional, enum: ASC | DESC
  status: Sort;       // optional, enum: ASC | DESC
  deadline: Sort;     // optional, enum: ASC | DESC
};
```
- **Response:**
```ts
results: Task[];
total: number;
```

**Create**
- **Route:** `api/task/create`
- **Method:** `POST`
- **JWT:** `mandatory`
- **Body:**
```ts
title: string;        // required
description: string;  // required
deadline: Date;       // required
```
- **Response:**
```ts
data: Task
```

**Update**
- **Route:** `api/task/update/:id`
- **Method:** `POST`
- **JWT:** `mandatory`
- **Param:** `id`
- **Body:**
```ts
title: string;        // optional
description: string;  // optional
deadline: Date;       // optional
status: ETaskStatus   // optional, enum: DONE|NOT_YET
```
- **Response:**
```ts
data: Task
```

## Utils Package

- `core` : Shared resource ( utils, interfaces, config, common modules, middlewares, ...)
- `modules` : Service modules


### Shared Resource Package

- `core` : A folder to manage shared resources of application

  ```shell

  ├── core                      # Shared Resource
  │   ├── common                # Common resources ( Dto, Constants, Decorator )
  │   ├── config                # Application config ( Env )
  │   ├── database              # Database ( Manage database config, entities, migrations )
  │   ├── enums                 # Manage enums
  │   ├── interfaces            # Manage interfaces
  │   ├── middlewares           # Manage middlewares
  │   ├── utils                 # Shared utilities

  ```

### Service modules

- `components` : A component structure that includes utilities and configuration

```shell
├── ...
├── task                         # Module Folder
│   ├── dto                      # DTO validation
│   ├───── query.dto.ts
│   ├───── mutation.dto.ts
│   ├───── index.ts
│   ├── services                 # Module service
│   ├───── task.service.ts       # Service
│   ├───── index.ts
│   ├── controllers              # Module controller
│   ├───── task.controller.ts    # Controller
│   ├───── index.ts
│   ├── task.module.ts           # Module
│   ├── index.ts
├── ...
```

---
