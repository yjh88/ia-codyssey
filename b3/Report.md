## 자동화 도구 비교 구현 (Make vs Zapier)

**주제:** 주식 뉴스 자동화  
**워크플로우:** "Google News RSS 수집 → 키워드 필터링 → Google Sheets 기록 + Gmail 이메일 알림"

---

# 🔧 Make 워크플로우 구현

## 프로젝트: 주식 뉴스 자동화
**워크플로우:** Google News RSS 수집 → 키워드 필터링 → Google Sheets 기록 + Gmail 이메일 알림

---

## 📌 워크플로우 흐름도

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│  1. TRIGGER                                         │
│  └─ RSS Feed – Watch RSS Feed Items                 │
│      · Google News RSS URL 입력                     │
│      · 실행 주기: 15분마다                           │
│                                                     │
│              ↓                                      │
│                                                     │
│  2. ACTION 1                                        │
│  └─ Google Sheets – Search Rows                     │
│      · 시트명: 키워드목록                            │
│      · 삼성 / SK하이닉스 / LG 등 키워드 불러오기    │
│                                                     │
│              ↓                                      │
│                                                     │
│  3. FILTER (Router – 조건 분기)                     │
│  ├─ [경로 A] 제목에 "삼성" 포함 → Samsung 시트      │
│  ├─ [경로 B] 제목에 "SK하이닉스" 포함 → SK 시트     │
│  └─ [경로 C] 기타 키워드 포함 → Other 시트          │
│                                                     │
│              ↓                                      │
│                                                     │
│  4. ACTION 2                                        │
│  └─ Google Sheets – Add a Row                       │
│      · 날짜 / 제목 / 링크 / 키워드 / 출처 기록      │
│                                                     │
│              ↓                                      │
│                                                     │
│  5. ACTION 3                                        │
│  └─ Email (SMTP) – Send an Email                    │
│      · 수신: yangjh5464@gmail.com                   │
│      · 제목: [주식뉴스] 키워드 매칭 뉴스 도착       │
│      · 내용: 뉴스 제목 + 링크 포함                  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## 🛠️ Make 모듈 설정 상세

### STEP 1 – TRIGGER: RSS Feed
| 항목 | 설정값 |
|------|--------|
| 모듈 | RSS Feed – Watch RSS Feed Items |
| URL | `https://news.google.com/rss/search?q=삼성+주식&hl=ko` |
| 실행 주기 | 15분 |
| Maximum | 10 |

### STEP 2 – ACTION 1: Google Sheets (키워드 읽기)
| 항목 | 설정값 |
|------|--------|
| 모듈 | Google Sheets – Search Rows |
| Spreadsheet | 뉴스_자동화_기록 |
| Sheet | 키워드목록 |
| Filter | (전체 읽기) |

### STEP 3 – FILTER: Router
| 경로 | 조건 |
|------|------|
| 경로 A | `{{1.title}}` contains `삼성` |
| 경로 B | `{{1.title}}` contains `SK하이닉스` |
| 경로 C | 나머지 (Else) |

### STEP 4 – ACTION 2: Google Sheets (데이터 쓰기)
| 항목 | 설정값 |
|------|--------|
| 모듈 | Google Sheets – Add a Row |
| Spreadsheet | 뉴스_자동화_기록 |
| Sheet | 뉴스기록 (경로별 Samsung/SK/Other) |
| A열 | `{{now}}` (날짜) |
| B열 | `{{1.title}}` (제목) |
| C열 | `{{1.url}}` (링크) |
| D열 | `삼성` / `SK하이닉스` / `기타` |
| E열 | `{{1.author}}` (출처) |

### STEP 5 – ACTION 3: Email (SMTP)
| 항목 | 설정값 |
|------|--------|
| 모듈 | Email – Send an Email |
| Connection | Others (SMTP) |
| SMTP Server | smtp.gmail.com |
| Port | 465 (SSL) |
| To | yangjh5464@gmail.com |
| Subject | `[주식뉴스] {{1.title}}` |
| Content | `제목: {{1.title}}\n링크: {{1.url}}\n날짜: {{now}}` |

---

## ⚠️ Make 사용 시 주의사항

- Gmail 직접 연결 불가 → **SMTP 방식** 사용 필요
- 앱 비밀번호 16자리 발급 필수 (Google 계정 2단계 인증 필요)
- Router 분기 시 각 경로마다 별도 모듈 연결 필요
- 무료 플랜: **월 1,000 Ops** 제한

---

## 😓 Make 사용 후기

> Make는 시각적 노드 구조로 전체 흐름을 한눈에 볼 수 있지만,  
> Gmail 연결 오류, Router 설정 복잡도 등 **초보자에게 진입 장벽이 높았음.**  
> 특히 SMTP 설정 과정에서 restricted scope 오류가 발생하여 추가 설정이 필요했음.



# ⚡ Zapier 워크플로우 구현

## 프로젝트: 주식 뉴스 자동화
**워크플로우:** Google News RSS 수집 → 키워드 필터링 → Google Sheets 기록 + Gmail 이메일 알림

---

## 📌 워크플로우 흐름도

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│  1. TRIGGER                                         │
│  └─ RSS by Zapier – New Item in Feed                │
│      · Feed URL: Google News RSS 주소 입력          │
│      · 새 뉴스 항목 감지 시 자동 실행              │
│                                                     │
│              ↓                                      │
│                                                     │
│  2. ACTION 1                                        │
│  └─ Google Sheets – Lookup Spreadsheet Row          │
│      · 시트: 키워드목록                             │
│      · 키워드 목록 조회 (삼성, SK하이닉스 등)       │
│                                                     │
│              ↓                                      │
│                                                     │
│  3. FILTER                                          │
│  └─ Filter by Zapier                                │
│      · 조건: 뉴스 제목에 키워드 포함 시에만 통과   │
│      · (title) Contains (삼성 OR SK하이닉스 OR LG)  │
│                                                     │
│              ↓                                      │
│                                                     │
│  4. ACTION 2                                        │
│  └─ Google Sheets – Create Spreadsheet Row          │
│      · 날짜 / 제목 / 링크 / 키워드 / 출처 기록     │
│                                                     │
│              ↓                                      │
│                                                     │
│  5. ACTION 3                                        │
│  └─ Gmail – Send Email                              │
│      · 수신: yangjh5464@gmail.com                   │
│      · 제목: [주식뉴스] 키워드 매칭 뉴스 도착      │
│      · 내용: 뉴스 제목 + 링크 포함                 │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## 🛠️ Zapier 모듈 설정 상세

### STEP 1 – TRIGGER: RSS by Zapier
| 항목 | 설정값 |
|------|--------|
| 앱 | RSS by Zapier |
| 이벤트 | New Item in Feed |
| Feed URL | `https://news.google.com/rss/search?q=삼성+주식&hl=ko` |
| 실행 방식 | 새 항목 감지 시 자동 실행 |

### STEP 2 – ACTION 1: Google Sheets (키워드 조회)
| 항목 | 설정값 |
|------|--------|
| 앱 | Google Sheets |
| 이벤트 | Lookup Spreadsheet Row |
| Spreadsheet | 뉴스_자동화_기록 |
| Worksheet | 키워드목록 |
| Lookup Column | A열 (키워드) |

### STEP 3 – FILTER: Filter by Zapier
| 항목 | 설정값 |
|------|--------|
| 앱 | Filter by Zapier |
| 조건 필드 | `1. Title` (RSS 제목) |
| 조건 | Contains |
| 값 | `삼성` |
| 추가 조건 | OR `SK하이닉스` / OR `LG` |

> 💡 **Zapier 팁:** Filter는 조건 불만족 시 자동으로 Zap 중단 → 별도 분기 설정 불필요!

### STEP 4 – ACTION 2: Google Sheets (데이터 기록)
| 항목 | 설정값 |
|------|--------|
| 앱 | Google Sheets |
| 이벤트 | Create Spreadsheet Row |
| Spreadsheet | 뉴스_자동화_기록 |
| Worksheet | 뉴스기록 |
| A열 | `Trigger Date` (날짜) |
| B열 | `1. Title` (뉴스 제목) |
| C열 | `1. Link` (뉴스 링크) |
| D열 | `1. Source` (출처) |
| E열 | 키워드 (텍스트 직접 입력) |

### STEP 5 – ACTION 3: Gmail
| 항목 | 설정값 |
|------|--------|
| 앱 | Gmail |
| 이벤트 | Send Email |
| To | `yangjh5464@gmail.com` |
| Subject | `[주식뉴스] 1. Title` |
| Body | `제목: 1. Title` `링크: 1. Link` `날짜: Trigger Date` |
| 연결 방식 | Google 계정 직접 로그인 (OAuth) ← **Make보다 훨씬 간단!** |

---

## ✅ Zapier 사용 시 장점

- Gmail 앱 **직접 OAuth 연결** → SMTP 설정 불필요 ⭐
- Filter 조건 설정이 **드롭다운 선택**으로 직관적 ⭐
- 단계별 리스트 구조로 **흐름 파악 쉬움** ⭐
- 테스트 버튼으로 **각 단계 즉시 확인** 가능 ⭐
- 무료 플랜: **월 100 Tasks** (단순 워크플로우에 충분)

---

## 😊 Zapier 사용 후기

> Make 대비 설정 과정이 훨씬 직관적이었음.  
> 특히 Gmail 연결이 Google 계정 로그인만으로 완료되어  
> **SMTP 오류 없이 바로 이메일 발송 테스트 성공.**  
> 초보자에게는 **Zapier가 더 적합**하다고 판단됨.



---
# 📊 Make vs Zapier 도구 비교 (주식 뉴스 자동화 프로젝트 기준)

> 직접 두 도구를 모두 사용해본 후 작성한 **실사용 비교 리포트**입니다.

---

## 🔄 구현 워크플로우 요약

| 구분 | 내용 |
|------|------|
| **주제** | 주식 뉴스 자동화 수집 및 알림 |
| **흐름** | RSS 수집 → 키워드 필터 → Sheets 기록 → Gmail 알림 |
| **사용 도구** | Make (시도) / Zapier (완성) |

---

## ⚙️ 구현 방식 비교

### [Make 구현]
```
TRIGGER  →  ACTION 1  →  FILTER(Router)  →  ACTION 2  →  ACTION 3
RSS 수집     Sheets 읽기   키워드 분기        Sheets 쓰기   SMTP 이메일
```
- Trigger : RSS Feed – Watch RSS Feed Items
- Action 1 : Google Sheets – Search Rows (키워드 목록 읽기)
- Filter   : Router – 삼성 / SK하이닉스 / 기타 3경로 분기
- Action 2 : Google Sheets – Add a Row (뉴스 데이터 기록)
- Action 3 : Email (SMTP) – Send an Email (Gmail SMTP 수동 설정)

### [Zapier 구현]
```
TRIGGER  →  ACTION 1  →  FILTER  →  ACTION 2  →  ACTION 3
RSS 수집     Sheets 조회   키워드 조건  Sheets 기록   Gmail 발송
```
- Trigger : RSS by Zapier – New Item in Feed
- Action 1 : Google Sheets – Lookup Spreadsheet Row (키워드 조회)
- Filter   : Filter by Zapier – title Contains 키워드
- Action 2 : Google Sheets – Create Spreadsheet Row (뉴스 기록)
- Action 3 : Gmail – Send Email (Google 계정 직접 연결)

---

## 📋 Make vs Zapier 항목별 상세 비교표

| 비교 항목 | Make | Zapier | 우위 |
|-----------|------|--------|------|
| **UI/UX** | 시각적 노드(버블) 기반 | 리스트(단계) 기반 | ⭐ Zapier |
| **설정 난이도** | 학습 곡선 있음, 복잡 | 직관적, 빠른 설정 | ⭐ Zapier |
| **조건 분기** | Router로 다중 경로 가능 | Filter로 단순 조건 처리 | ⭐ Make |
| **Gmail 연결** | SMTP 수동 설정 필요 | OAuth 직접 로그인 | ⭐ Zapier |
| **무료 플랜** | 1,000 Ops/월 | 100 Tasks/월 | ⭐ Make |
| **실행 주기** | 최소 15분 간격 | 최소 15분 간격 | 동일 |
| **실행 로그** | 단계별 데이터 상세 확인 | Task History 확인 | ⭐ Make |
| **RSS 지원** | RSS Feed 모듈 기본 제공 | RSS by Zapier 제공 | 동일 |
| **Google Sheets** | 읽기/쓰기/검색 지원 | 읽기/쓰기/검색 지원 | 동일 |
| **오류 발생** | Gmail restricted scope 오류 | 오류 없이 바로 연결 | ⭐ Zapier |
| **초보자 친화도** | 낮음 | 높음 | ⭐ Zapier |
| **적합 사용자** | 복잡한 분기 워크플로우 | 빠른 단순 자동화 | 상황에 따라 다름 |

---

## 🏆 최종 선택: Zapier

### 선택 이유
```
1. Gmail 연결이 OAuth 방식으로 즉시 완료 (Make는 SMTP 오류 발생)
2. 단계별 리스트 UI가 워크플로우 흐름 파악에 더 직관적
3. Filter 설정이 드롭다운 선택으로 간단하게 완료
4. 각 단계 테스트 버튼으로 즉시 결과 확인 가능
5. 초보자 입장에서 전체 설정 시간이 Make 대비 절반 이하
```

### Make를 선택할 경우
```
1. 복잡한 다중 분기(Router) 처리가 필요한 경우
2. 무료 플랜에서 더 많은 실행 횟수가 필요한 경우 (1,000 Ops)
3. 단계별 데이터 흐름을 시각적으로 디버깅하고 싶은 경우
```

---

## 💬 직접 사용 후기

> **Make:** 시각적으로 전체 흐름을 한눈에 볼 수 있다는 장점이 있지만,  
> Gmail 연결 시 `restricted scope` 오류가 발생하여 SMTP 수동 설정이 필요했고,  
> Router 분기마다 별도 모듈을 연결해야 해서 설정이 복잡했습니다.

> **Zapier:** Google 계정 로그인만으로 Gmail 연결이 즉시 완료되었고,  
> 단계별 리스트 구조 덕분에 처음 사용하는 사람도 쉽게 따라할 수 있었습니다.  
> **초보자에게는 Zapier가 훨씬 접근하기 쉬운 도구**라고 생각합니다.




