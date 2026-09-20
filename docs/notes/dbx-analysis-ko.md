# DBX 전수조사 분석 리포트 (한국어)

> 작성일: 2026-09-20
> 분석 대상 버전: `0.6.16`
> 정리: Claude Code 세션 대화 내용 요약

## 🔗 저장소 주소

- **현재 저장소(포크)**: https://github.com/bmshin94/dbx
- **원본 저장소(업스트림)**: https://github.com/t8y2/dbx
- **공식 문서**: https://dbxio.com/en/docs/what-is-dbx
- **플러그인 스토어**: https://github.com/t8y2/dbx-store
- **npm 패키지**
  - MCP 서버: https://www.npmjs.com/package/@dbx-app/mcp-server
  - CLI: https://www.npmjs.com/package/@dbx-app/cli
  - 플러그인 CLI: https://www.npmjs.com/package/@dbx-app/plugin-cli

---

## 1. 이 프로젝트는 무엇인가

**DBX**는 Rust + Tauri 기반의 오픈소스 **데이터베이스 관리 GUI 툴**이다.
DBeaver / Navicat / TablePlus / DataGrip 의 경쟁 제품에 해당한다.

| 항목 | 값 |
|---|---|
| 라이선스 | Apache-2.0 (상업적 이용 가능) |
| 버전 | 0.6.16 |
| 설치 크기 | 약 25 MB (Java/Python/Chromium 미포함) |
| 지원 DB | 90종 이상 |
| 전체 파일 수 | 5,406개 (모노레포) |
| 배포 형태 | 데스크톱(macOS/Windows/Linux), Docker/Web, CLI, MCP 서버 |

### 핵심 차별점 3가지

1. **경량** — Java 런타임이나 번들 Chromium 없이 25MB 단일 바이너리
2. **90+ DB 지원** — 중국 국산 DB(达梦/金仓/OceanBase/GaussDB 등)까지 광범위 커버
3. **AI 네이티브** — 앱 내장 AI SQL 어시스턴트 + 별도 MCP 서버 제공

---

## 2. 폴더 구조 (전수조사 결과)

| 경로 | 역할 | 규모 |
|---|---|---|
| `crates/dbx-core/` | 핵심 엔진: DB 드라이버, 쿼리/스키마 로직, import/export, 데이터 전송 | Rust 422,108줄 / 378파일 |
| `crates/dbx-mcp/` | MCP 서버 (AI 에이전트용 18개 툴) | Rust 10,992줄 / 19파일 |
| `crates/dbx-web/` | Docker/Web 백엔드 서비스 (Axum) | Rust 23,522줄 / 54파일 |
| `crates/dbx-cli/` | 터미널용 `dbx` CLI | Rust 1,348줄 |
| `crates/dbx-sqlite-worker/` | SQLite 워커 프로세스 | Rust 565줄 |
| `apps/desktop/` | 프론트엔드 UI (Vue 3 + TypeScript) | .vue 477개 / .ts 2,133개 |
| `src-tauri/` | Tauri 2 셸 (데스크톱 패키징) | — |
| `agents/` | JDBC/네이티브 에이전트 드라이버 (Java/Go, JSON-RPC 2.0 stdin/stdout) | 45종+ |
| `plugins/` | 플러그인 플랫폼 (manifest v1, Rust/Go SDK, `.dbxp` 패키저, Ed25519 서명) | — |
| `skills/dbx/SKILL.md` | Claude용 Skill 정의 (dbx CLI 사용 지침) | 1파일 |
| `packages/` | npm 배포 패키지 25종 (CLI/MCP/plugin-cli + 플랫폼별 바이너리) | — |
| `docs/` | 공식 문서 사이트 (Next.js, 영/중 다국어 MDX) | — |
| `deploy/` | Docker, 1Panel, Homebrew, PHP 터널 스크립트 | — |
| `examples/` | CLI / MCP / Docker / Web API 샘플 | — |
| `.github/workflows/` | CI/CD 파이프라인 | 30개 |

---

## 3. 주요 기능

### 데이터베이스 지원 (90+)
MySQL, PostgreSQL, SQLite, Cloudflare D1, Redis, MongoDB, DuckDB, ClickHouse,
SQL Server, Oracle, Elasticsearch, Meilisearch, Qdrant, Milvus, Weaviate,
MariaDB, TiDB, OceanBase, openGauss, GaussDB, KingbaseES, Doris, StarRocks,
Redshift, DM(达梦), TDengine, CockroachDB, InfluxDB 등.
에이전트 기반으로 H2, Snowflake, Trino, Hive, DB2, Neo4j, Cassandra,
BigQuery, Cloud Spanner, Databricks, SAP HANA, Teradata 등 추가 지원.
메시지 큐 관리(Pulsar, Kafka, RocketMQ)도 포함.

### 기능 카테고리
- **쿼리 에디터** — CodeMirror 6, 메타데이터 기반 자동완성, 9종 테마, 쿼리 히스토리
- **AI SQL 어시스턴트** — 자연어 → SQL, 쿼리 설명/최적화/오류 수정, 실행 전 안전성 검사
- **데이터 그리드** — 가상 스크롤, 인라인 편집, 저장 전 SQL 미리보기, CSV/JSON/Markdown/XLSX/INSERT 내보내기
- **스키마 도구** — 스키마 브라우저, 객체 브라우저, 테이블 구조 편집기, ER 다이어그램, 스키마 diff, 실행 계획, 필드 리니지
- **데이터 작업** — 테이블 import(CSV/Excel), DB 간 데이터 전송, 전체 덤프, 데이터 비교, .sql 파일 실행, Parquet/CSV/JSON 미리보기(DuckDB), DBeaver/Navicat 접속정보 import
- **전용 브라우저** — Redis(키 패턴 검색, 배치 작업, TTL 편집), MongoDB(문서 CRUD, Atlas/replica set)
- **안전성/연결** — SSH 터널, 프록시 설정, 자동 재연결, 파괴적 작업 확인 다이얼로그, 암호화된 설정 export/import

---

## 4. MCP 서버 상세 (AI 연동의 핵심)

### 아키텍처
```
@dbx-app/mcp-server
└── 소형 Node.js 런처
    └── 플랫폼별 Rust dbx-mcp 바이너리
        └── dbx-core 데이터베이스/에이전트 인프라
```

### 설치
```bash
npx @dbx-app/mcp-server
```

`.mcp.json` 설정:
```json
{
  "mcpServers": {
    "dbx": { "command": "npx", "args": ["-y", "@dbx-app/mcp-server"] }
  }
}
```

Web/Docker 모드:
```json
{
  "mcpServers": {
    "dbx": {
      "command": "npx",
      "args": ["-y", "@dbx-app/mcp-server"],
      "env": {
        "DBX_WEB_URL": "http://localhost:4224",
        "DBX_WEB_PASSWORD": "your-web-login-password"
      }
    }
  }
}
```

### 제공 MCP 툴 18종

| 툴 | 설명 |
|---|---|
| `dbx_list_connections` | 접속 목록 조회 |
| `dbx_list_databases` | 접속의 데이터베이스 목록 (MCP 스코프 적용) |
| `dbx_add_connection` | 접속 추가 |
| `dbx_duplicate_connection` | 접속 복제 |
| `dbx_remove_connection` | 접속 삭제 |
| `dbx_list_tables` | 테이블/뷰/컬렉션/MQ 토픽 목록 |
| `dbx_describe_table` | 컬럼 및 테이블 메타데이터 |
| `dbx_list_routines` | 저장 프로시저/함수 목록 |
| `dbx_get_routine_source` | 프로시저/함수 소스 조회 |
| `dbx_get_schema_context` | **AI 프롬프트용 압축 스키마 컨텍스트** |
| `dbx_execute_query` | SQL 또는 MongoDB 셸 명령 실행 (최대 100행) |
| `dbx_execute_batch` | 다중 문장 SQL 스크립트 실행 |
| `dbx_open_session` | 상태 유지 SQL 세션 열기 |
| `dbx_close_session` | 세션 종료 |
| `dbx_execute_redis_command` | Redis 명령 실행 |
| `dbx_peek_messages` | Kafka 메시지 조회 (offset 커밋 없음) |
| `dbx_send_message` | MQ 토픽으로 메시지 전송 |
| `dbx_open_table` | DBX 데스크톱에서 테이블 열기 |
| `dbx_execute_and_show` | 쿼리 실행 후 데스크톱에 결과 표시 |

### 권한 모델 (3단계)
DBX 설정 → MCP 에서 관리. 머신 판독 값은 다음과 같다.

| 값 | 의미 |
|---|---|
| `read_only` | 읽기 전용 |
| `safe_write` | 데이터 읽기/쓰기 |
| `high_risk_write` | 전체 권한 |

- 접속 allowlist로 AI가 볼 수 있는 접속을 제한
- 연결 스코핑이 켜지면 접속 변경 툴과 데스크톱 UI 툴은 **목록에서 아예 숨겨짐**
- `DBX_MCP_ALLOW_WRITES=0` 은 하위호환용이며 쓰기를 활성화할 수는 없음

### 접속 저장소 경로
| OS | 경로 |
|---|---|
| macOS | `~/Library/Application Support/com.dbx.app/dbx.db` |
| Linux | `~/.local/share/com.dbx.app/dbx.db` |
| Windows | `%APPDATA%\com.dbx.app\dbx.db` |

`DBX_DATA_DIR` 환경변수로 재지정 가능.

---

## 5. 설치 및 사용법

### 데스크톱 앱
```bash
# macOS
brew install --cask dbx

# Windows
winget install t8y2.dbx
scoop bucket add dbx https://github.com/t8y2/scoop-bucket && scoop install dbx

# Linux
flatpak remote-add --if-not-exists flatpark https://dl.flatpark.org/flatpark.flatpakrepo
flatpak install flatpark com.dbxio.dbx
```

### Docker / 셀프호스팅
```bash
docker run -d --pull=always --name dbx -p 4224:4224 -v dbx-data:/app/data t8y2/dbx:latest
# 브라우저에서 http://localhost:4224
```

```bash
docker compose -f deploy/docker-compose.release.yml up -d
```

리버스 프록시 하위 경로 배포 시 `DBX_PUBLIC_BASE_PATH=/dbx` 설정.

### CLI
```bash
npm install -g @dbx-app/cli
# 또는
brew tap t8y2/tap && brew install dbx-cli
```

| 명령 | 설명 |
|---|---|
| `dbx doctor` | 로컬 설정 및 데스크톱 브리지 진단 |
| `dbx capabilities` | 직접 실행 / 브리지 필요 DB 확인 |
| `dbx connections list --json` | 접속 목록 (비밀정보 미출력) |
| `dbx schema list <connection>` | 테이블/뷰 목록 |
| `dbx schema describe <connection> <table>` | 컬럼 조회 |
| `dbx query <connection> "<sql>"` | SQL 실행 |
| `dbx context <connection>` | 프롬프트용 압축 스키마 출력 |
| `dbx open <connection> <table>` | 데스크톱에서 테이블 열기 |

안전 게이트: 쓰기 작업은 `--allow-writes`, DROP/TRUNCATE/ALTER는 `--allow-writes` + `--allow-dangerous-sql` 둘 다 필요.

### 소스 빌드 (개발자)
사전 요구: Node.js >= 18(권장 22.13+), pnpm, Rust >= 1.88

```bash
make              # Tauri 데스크톱 개발 모드
make dev-fast     # DuckDB 제외 (빌드 시간 단축)
make dev-web      # 웹 프론트엔드
make dev-backend  # 웹 백엔드
make docs         # 문서 사이트
make package      # 설치 패키지 빌드
```

---

## 6. 플러그인 / 스킬 / MCP 구분

| 구분 | 위치 | 정체 | 소비 주체 |
|---|---|---|---|
| 본체 앱 | `apps/`, `src-tauri/` | Tauri 데스크톱 애플리케이션 | 사람 |
| MCP 서버 | `crates/dbx-mcp/` | 18개 툴 제공, 실제 DB 실행 통로 | AI 에이전트 |
| Skill | `skills/dbx/SKILL.md` | dbx CLI 사용법을 Claude에게 지시하는 문서 | Claude |
| 플러그인 | `plugins/` | DBX 앱 자체를 확장하는 `.dbxp` 패키지 | DBX 앱 |

즉 **넷 다 존재**하며, 본체는 앱이고 나머지는 확장 레이어다.

### 플러그인 플랫폼 요약
- 계약: manifest v1 + Host API 1.x + 사이드카 프로토콜 v1
- SDK: Rust, Go / 템플릿: `frontend`, `rust`, `go`
- 생성: `dbx-plugin create my-plugin --template frontend`
- 공식 카탈로그: `https://raw.githubusercontent.com/t8y2/dbx-store/main/catalog/index.json`
- 보안: Ed25519 서명, 카탈로그 4 MiB / 패키지 512 MiB 제한, SHA-256 검증, Manifest ID·버전·퍼블리셔·권한·키 ID 일치 검증 후 활성화

### Skill 파일에서 주목할 설계
`skills/dbx/SKILL.md` 는 AI 안전 설계의 좋은 예시다.

> "절대 dbx CLI를 우회하지 말 것. `SQL_BLOCKED` 또는 오류 발생 시 Python sqlite3, 셸 리다이렉트 등 다른 수단으로 DB에 직접 접근하지 말고, CLI가 반환한 내용을 사용자에게 알리고 판단을 맡길 것."

---

## 7. API 토큰이 필요한가

| 기능 | 토큰 필요 |
|---|---|
| DB 접속 / 쿼리 / 스키마 조회 | 불필요 |
| ER 다이어그램, export, 데이터 전송 | 불필요 |
| MCP 서버 / CLI | 불필요 (로컬 `dbx.db` 직접 읽음) |
| AI SQL 어시스턴트 | **필요** (OpenAI / Claude 등 본인 API 키) |

Ollama 등 로컬 모델을 연결하면 AI 기능도 API 키 없이 사용 가능하다.
Web/Docker 배포 시에는 `DBX_WEB_PASSWORD` 로 로그인 보호.

---

## 8. GitHub에서 유명한 이유 분석

1. **"25MB" 한 문장의 마케팅 파워** — DBeaver(Java 필요, 500MB+), DataGrip(1GB+) 대비 명확한 수치 차별화
2. **중국 시장 특화** — 达梦, 金仓, OceanBase, GaussDB, TDengine, Doris, StarRocks 등 국산 DB 지원. QQ/WeChat/Feishu 커뮤니티 운영
3. **AI 타이밍** — MCP를 초기에 제대로 구현한 DB 툴
4. **완전 무료 + Apache-2.0** — Navicat/TablePlus 유료 모델 대비 우위
5. **노출 채널 관리** — Trendshift, HelloGitHub, Product Hunt, MCP Toplist 등재. 스폰서 7개사 확보
6. **높은 엔지니어링 품질** — CI 워크플로우 30개, 테스트 200개+, 3개국어 문서, Windows 7 지원을 위해 `wry`/`tiberius`/`rumqttc`/`ctor`/`dirs-sys` 등 의존성을 직접 포크·패치

---

## 9. 로컬 에이전트 구축 관점의 가치

### 그대로 활용하는 경우
```
Ollama (로컬 LLM)
   ↕
Claude Code / Cursor
   ↕ MCP
DBX MCP Server (Rust 단일 바이너리, Node 불필요)
   ↕
MySQL / PostgreSQL / MongoDB / Redis ...
```
완전 오프라인, 데이터 외부 유출 없는 DB 에이전트 환경 구성이 가능하다.

### 설계를 차용하는 경우 (더 큰 가치)

| 배울 점 | DBX 내 위치 |
|---|---|
| 3단계 권한 모델 | `read_only` / `safe_write` / `high_risk_write` |
| 툴 분해 설계 | 18개 MCP 툴 (스키마 / 쿼리 / 세션 / UI 분리) |
| 토큰 절감 기법 | `dbx_get_schema_context` — 스키마를 AI용으로 압축 |
| 이중 안전 게이트 | `--allow-writes` + `--allow-dangerous-sql` |
| 상태 유지 세션 | `dbx_open_session` / `dbx_close_session` |
| 툴 스코핑 | 스코프 활성화 시 위험 툴을 목록에서 제거 |
| Skill 작성법 | `skills/dbx/SKILL.md` — 금지사항 명시 방식 |
| 사이드카 프로세스 설계 | `agents/` — stdin/stdout JSON-RPC 2.0 |

### 한계
- MCP가 접속 정보를 DBX 로컬 저장소(`dbx.db`)에서 읽으므로 DBX 설치가 전제
- 완전 커스텀 에이전트를 만들 경우 접속 관리 계층은 별도 구현 필요

---

## 10. 수익화 아이디어

> 전제: Apache-2.0 이므로 포크/개조/상업적 판매 모두 합법(라이선스 고지 유지 필요).
> 단, 원본이 무료이므로 "복제 후 판매"는 경쟁력이 없다. **원본이 하지 않는 영역**을 노린다.

### 1순위 — 플러그인 판매 (난이도 중 / 수익 중)
`plugins/` 에 마켓플레이스 구조가 이미 완성되어 있고, 프론트엔드 전용 플러그인도 가능하다.

| 플러그인 | 타겟 | 예시 가격 |
|---|---|---|
| 개인정보 마스킹 | 주민번호/전화/카드 자동 마스킹, 개인정보보호법 대응 | ₩50,000/년 |
| 한국 공공 DB 커넥터 | Tibero, Altibase 지원 | ₩150,000/년 |
| 한글 인코딩 마법사 | EUC-KR ↔ UTF-8 깨짐 복구 | ₩30,000/년 |
| 쿼리 감사 로그 | 실행 이력 기록, ISMS 대응 | ₩100,000/년 |

### 2순위 — 컨설팅 / SI (난이도 낮 / 수익 높)
"AI 기반 DB 관리 환경 구축" 패키지.

| 항목 | 금액 |
|---|---|
| DBX 설치 + Docker 팀 배포 | ₩500,000 |
| MCP 서버 세팅 + AI 연동 | ₩1,000,000 |
| 사내 Ollama 로컬 LLM 구축 | ₩2,000,000 |
| 개발자 교육 4시간 | ₩500,000 |
| **합계** | **₩4,000,000** |

타겟: 금융·의료·공공 등 데이터 외부 반출이 제한되는 조직.

### 3순위 — MCP 기반 SaaS (난이도 높 / 수익 최고)
Slack 봇 형태의 사내 Text-to-SQL 어시스턴트.

- 구성: Slack Bot + Claude API + DBX MCP + `read_only` 강제
- 가격: 월 ₩99,000(5인) / ₩299,000(20인) / 엔터프라이즈 별도
- 가치 제안: 비개발 직군의 데이터 추출 요청이 개발자에게 몰리는 문제 해소

### 4순위 — 교육 콘텐츠 (난이도 중 / 수익 중)

| 상품 | 채널 | 가격 |
|---|---|---|
| MCP 서버 직접 만들기 | 인프런/유데미 | ₩77,000 |
| Rust + Tauri 데스크톱 앱 개발 | 인프런 | ₩99,000 |
| AI 에이전트에 DB 연결하기 (전자책) | 크몽/부크크 | ₩29,000 |
| DBX 코드 분석 시리즈 | YouTube | 광고/협찬 |

### 5순위 — 한국 특화 포크 (난이도 중상 / 수익 중)
- 차별화: Tibero/Altibase/CUBRID 기본 지원, 완전 한글 UI, 국내 클라우드 원클릭 연결, 토스/카카오페이 결제, 국내 기술지원
- 모델: Free / Pro ₩49,000/년 / Team ₩199,000/년
- 준수사항: LICENSE·NOTICE 유지, 변경사항 명시, "DBX" 상표 미사용

### 추천 로드맵
| 시점 | 실행 항목 |
|---|---|
| 1개월 | DBX 숙달 + 분석 콘텐츠 발행으로 신뢰도 확보 |
| 2개월 | 플러그인 1종 출시 |
| 3개월 | 컨설팅 1건 수주 |
| 6개월 | Slack DB 봇 SaaS MVP 런칭 |
| 12개월 | 법인 설립 및 한국 특화 포크 검토 |

자본이 필요 없는 2순위(컨설팅) + 4순위(교육) 부터 시작하는 것이 현실적이다.

---

## 11. React / PHP 로 만들 수 있는가

### 불가능하거나 매우 어려운 부분

| 항목 | 사유 |
|---|---|
| 25MB 단일 실행파일 | Electron 기반은 150MB+ (Tauri는 시스템 WebView 사용) |
| 90종 네이티브 DB 드라이버 | Rust `sqlx` / `tiberius` / `redis-rs` / `mongodb` 생태계 의존 |
| 42만 줄 규모 | 개인 개발로는 수년 소요 |
| 대용량 그리드 네이티브 성능 | JS 런타임 한계 |

### 충분히 가능한 부분

**A. React + Node.js 웹 DB 매니저 (현실적 추천)**
```
프론트: React + TanStack Table + Monaco Editor
백엔드: Node.js(Express) + mysql2 / pg / mongodb
AI:     Claude API 연동 Text-to-SQL
```
MySQL/PostgreSQL/MongoDB 3종만으로도 상품성이 있으며, 웹 기반이라 협업 기능에서 오히려 유리하다.

**B. PHP(Laravel) 버전**
```
Laravel + Livewire + PDO → phpMyAdmin 현대화 + AI 기능
```
참고로 DBX 저장소에도 `deploy/dbx_tunnel.php` 가 존재한다(공유 호스팅 환경용 터널 스크립트).
국내 공유호스팅 시장은 여전히 PHP 비중이 높아 틈새가 존재한다.

**C. 가장 효율적인 방식 — 플러그인/프론트엔드만 제작**
```
DBX 본체(Rust)는 그대로 사용
→ React/Vue 로 플러그인 UI 제작
→ .dbxp 패키징 후 마켓 배포
```

### 결론
DBX 클론을 만들기보다, **DBX MCP 서버를 백엔드로 활용하는 React 웹앱**(예: 채팅형 AI 데이터 대시보드)을 만드는 편이
개발량 대비 차별화 효율이 가장 높다. 90종 DB 지원은 DBX가 담당하고, 차별화는 UI/UX 레이어에서 만든다.

---

## 12. 기술 스택 요약

| 레이어 | 기술 |
|---|---|
| 프레임워크 | Tauri 2 |
| 프론트엔드 | Vue 3 + TypeScript |
| UI | shadcn-vue + Tailwind CSS 4 |
| 에디터 | CodeMirror 6 |
| 백엔드 | Rust + sqlx / tiberius / redis-rs / mongodb |
| 웹 백엔드 | Axum (`crates/dbx-web`) |
| 패키지 매니저 | pnpm 10.27 / Cargo |
| 테스트 | Vitest (프론트), cargo test (Rust) |
| 린트/포맷 | oxlint, oxfmt, clippy, rustfmt |
| 에이전트 | Java(Gradle, JRE 21) + Go |

---

## 13. 참고 링크

- 저장소(포크): https://github.com/bmshin94/dbx
- 저장소(원본): https://github.com/t8y2/dbx
- 릴리즈: https://github.com/t8y2/dbx/releases
- 플러그인 개발 문서: https://dbxio.com/en/docs/plugin-development
- 플러그인 스토어: https://github.com/t8y2/dbx-store
- Docker Hub: https://hub.docker.com/r/t8y2/dbx
- Discord: https://discord.gg/W7NyVDRt6a
