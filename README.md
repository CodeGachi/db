# SyncNapse On-Premise Database & Storage

SyncNapse 프로젝트를 위한 PostgreSQL 데이터베이스와 MinIO 오브젝트 스토리지를 Docker Compose로 관리하는 인프라 설정입니다.

## 📦 구성 요소

| 서비스 | 이미지 | 포트 | 설명 |
|--------|--------|------|------|
| PostgreSQL | postgres:16-alpine | 5432 | 메인 데이터베이스 |
| MinIO | minio/minio:latest | 9000 (API), 9001 (Console) | 파일 스토리지 |

## 🚀 시작하기

### 1. 환경 변수 설정

```bash
cp .env.example .env
# .env 파일을 열어 필요한 값을 수정하세요
```

### 2. 컨테이너 실행

```bash
docker compose up -d
```

### 3. 상태 확인

```bash
docker compose ps
docker compose logs -f
```

## 📁 디렉토리 구조

```
.
├── docker-compose.yml    # Docker Compose 설정
├── .env                  # 환경 변수 (gitignore)
├── .env.example          # 환경 변수 예시
├── init/
│   └── 001_schema.sql    # 초기 데이터베이스 스키마
└── data/                 # 영속 데이터 (gitignore)
    ├── postgres/         # PostgreSQL 데이터
    └── minio/            # MinIO 데이터
```

## 📋 초기화 스키마 (init/001_schema.sql)

`init/001_schema.sql` 파일은 [SyncNapse](https://github.com/CodeGachi/SyncNapse) 프로젝트에서 Prisma를 사용하여 생성됩니다.

스키마 생성 방법:
```bash
# SyncNapse 프로젝트에서
npx prisma generate
npx prisma migrate diff --from-empty --to-schema-datamodel prisma/schema.prisma --script > 001_schema.sql
```

## 🔧 환경 변수

| 변수명 | 설명 | 기본값 |
|--------|------|--------|
| `POSTGRES_USER` | PostgreSQL 사용자명 | - |
| `POSTGRES_PASSWORD` | PostgreSQL 비밀번호 | - |
| `POSTGRES_DB` | 데이터베이스 이름 | - |
| `POSTGRES_PORT` | PostgreSQL 외부 포트 | 5432 |
| `MINIO_ROOT_USER` | MinIO 관리자 사용자명 | - |
| `MINIO_ROOT_PASSWORD` | MinIO 관리자 비밀번호 | - |
| `MINIO_SERVER_URL` | MinIO 서버 URL | - |
| `MINIO_BROWSER_REDIRECT_URL` | MinIO 콘솔 URL | - |
| `MINIO_API_PORT` | MinIO API 포트 | 9000 |
| `MINIO_CONSOLE_PORT` | MinIO 콘솔 포트 | 9001 |
| `STORAGE_BUCKET` | 기본 스토리지 버킷 이름 | - |

## 🔄 데이터베이스 강제 재초기화

기존 테이블이 있어도 강제로 초기화하려면:

```bash
FORCE_INIT=true docker compose up postgres-init
```

## 🛠️ 유용한 명령어

```bash
# 컨테이너 중지
docker compose down

# 컨테이너 및 볼륨 삭제 (데이터 포함)
docker compose down -v
rm -rf data/

# 로그 확인
docker compose logs postgres
docker compose logs minio

# PostgreSQL 접속
docker exec -it syncnapse-postgres psql -U $POSTGRES_USER -d $POSTGRES_DB
```

## 📌 접속 정보

- **PostgreSQL**: `postgresql://<user>:<password>@localhost:5432/<database>`
- **MinIO Console**: `http://localhost:9000`
- **MinIO API**: `http://localhost:9001`

## 📄 License

This project is part of [SyncNapse](https://github.com/CodeGachi/SyncNapse).

