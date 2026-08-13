# Zapier 구현 가이드

## 1. 준비물
- Zapier 계정
- Google Sheets 문서
- Gmail 또는 Slack 계정
- Google News RSS 주소

## 2. Google Sheets 준비
스프레드시트 이름은 `주식뉴스_자동수집`으로 만들고, 1행에 아래 컬럼을 입력한다.

| 수집시간 | 종목명 | 뉴스제목 | 키워드 | 뉴스링크 | 발행일 | 알림여부 |
|---|---|---|---|---|---|---|

## 3. 새 Zap 만들기
1. Zapier에 접속한다.
2. `Create Zap`을 클릭한다.
3. Trigger 앱으로 `RSS by Zapier`를 선택한다.
4. 이벤트는 `New Item in Feed`를 선택한다.

## 4. RSS Trigger 설정
Feed URL에 Google News RSS 주소를 입력한다.

예시:
```text
https://news.google.com/rss/search?q=삼성전자%20주식&hl=ko&gl=KR&ceid=KR:ko
```

여러 종목을 하나의 RSS로 보고 싶다면 아래처럼 사용할 수 있다.

```text
https://news.google.com/rss/search?q=삼성전자%20OR%20SK하이닉스%20주식&hl=ko&gl=KR&ceid=KR:ko
```

설정 후 `Test Trigger`를 눌러 뉴스 제목, 링크, 발행일이 불러와지는지 확인한다.

## 5. Filter 설정
다음 단계에서 `Filter by Zapier`를 추가한다. 조건에 맞는 뉴스만 다음 단계로 통과시킨다.

필터 조건 예시:
```text
Title contains 삼성전자
OR
Title contains SK하이닉스
OR
Title contains 실적
OR
Title contains 반도체
```

Zapier 무료 플랜에서는 복잡한 분기 기능이 제한될 수 있으므로 단순 필터 중심으로 구성하는 것이 좋다.

## 6. Google Sheets에 저장
Action 앱으로 `Google Sheets`를 선택하고 이벤트는 `Create Spreadsheet Row`를 선택한다.

매핑 예시:
| Google Sheets 컬럼 | Zapier 값 |
|---|---|
| 수집시간 | Zap 실행 시간 |
| 종목명 | 관심종목 |
| 뉴스제목 | RSS Title |
| 키워드 | 필터 키워드 |
| 뉴스링크 | RSS Link |
| 발행일 | RSS Published |
| 알림여부 | 발송 |

## 7. Gmail 또는 Slack 알림
마지막 단계에서 Gmail 또는 Slack을 선택한다.

Gmail:
```text
Gmail - Send Email
```

Slack:
```text
Slack - Send Channel Message
```

알림 메시지 예시:
```text
[주식뉴스 알림]

뉴스 제목: {{Title}}
링크: {{Link}}
발행일: {{Published}}

Google Sheets에 자동 기록되었습니다.
```

## 8. 테스트
1. `Test Trigger`로 RSS 데이터가 들어오는지 확인한다.
2. Filter 조건을 통과하는지 확인한다.
3. Google Sheets에 새 행이 추가되는지 확인한다.
4. Gmail 또는 Slack 알림이 도착하는지 확인한다.

## 9. 최종 Zap 구조
```text
RSS by Zapier - New Item in Feed
→ Filter by Zapier
→ Google Sheets - Create Spreadsheet Row
→ Gmail 또는 Slack 알림
```

## 10. Make와의 차이
Make는 Router를 이용한 여러 조건 분기에 강점이 있고, Zapier는 단계별 설정이 단순해서 초보자가 사용하기 쉽다. 복잡한 종목별 분기는 Make가 더 적합하고, 간단한 뉴스 수집 자동화는 Zapier로도 충분히 구현할 수 있다.
