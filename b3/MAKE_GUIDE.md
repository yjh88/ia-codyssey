# Make 구현 가이드

## 1. 준비물
- Make 계정
- Google Sheets 문서
- Gmail 또는 Slack 계정
- Google News RSS 주소

## 2. Google Sheets 만들기
스프레드시트 이름은 `주식뉴스_자동수집`으로 만들고, 1행에 아래 컬럼을 입력한다.

| 수집시간 | 종목명 | 뉴스제목 | 키워드 | 뉴스링크 | 발행일 | 알림여부 |
|---|---|---|---|---|---|---|

## 3. Scenario 생성
1. Make에 접속한다.
2. 왼쪽 메뉴에서 `Scenarios`를 클릭한다.
3. `Create a new scenario`를 클릭한다.
4. 가운데 `+` 버튼을 누른다.

## 4. RSS 모듈 추가
1. 검색창에 `RSS`를 입력한다.
2. `RSS - Watch RSS Feed Items`를 선택한다.
3. RSS URL을 입력한다.

예시:
```text
https://news.google.com/rss/search?q=삼성전자%20OR%20SK하이닉스%20주식&hl=ko&gl=KR&ceid=KR:ko
```

설정 예시:
| 항목 | 값 |
|---|---|
| URL | RSS 주소 |
| Maximum number of returned items | 5 또는 10 |

## 5. Router 추가
RSS 모듈 뒤에 `Router`를 추가한다. Router는 뉴스 제목이나 설명에 따라 삼성전자, SK하이닉스, 중요 키워드 뉴스로 나누는 역할을 한다.

```text
RSS → Router
```

## 6. 삼성전자 경로 설정
Router 오른쪽에 `Google Sheets - Add a Row` 모듈을 추가한다.

필터 조건:
```text
Title contains 삼성전자
OR
Description contains 삼성전자
```

Google Sheets 매핑:
| 컬럼 | 값 |
|---|---|
| 수집시간 | now |
| 종목명 | 삼성전자 |
| 뉴스제목 | RSS Title |
| 키워드 | 삼성전자 |
| 뉴스링크 | RSS Link |
| 발행일 | RSS Published |
| 알림여부 | 발송 |

## 7. SK하이닉스 경로 설정
같은 방식으로 두 번째 경로를 만든다.

필터 조건:
```text
Title contains SK하이닉스
OR
Description contains SK하이닉스
```

종목명과 키워드는 `SK하이닉스`로 입력한다.

## 8. 중요 키워드 경로 설정
세 번째 경로에는 중요 키워드 필터를 설정한다.

필터 조건 예시:
```text
Title contains 실적
OR
Title contains 급등
OR
Title contains 하락
OR
Title contains 반도체
OR
Description contains 실적
OR
Description contains 반도체
```

## 9. Gmail 또는 Slack 알림 추가
Google Sheets 모듈 뒤에 Gmail 또는 Slack 모듈을 연결한다.

Gmail 예시:
```text
Gmail - Send an Email
```

알림 메시지 예시:
```text
[주식뉴스 알림]

종목: 삼성전자
뉴스 제목: {{Title}}
링크: {{Link}}
발행일: {{Published}}

Google Sheets에 자동 기록되었습니다.
```

## 10. 테스트 및 실행
1. 왼쪽 아래 `Run once`를 클릭한다.
2. RSS 데이터가 들어오는지 확인한다.
3. Google Sheets에 행이 추가되는지 확인한다.
4. Gmail 또는 Slack 알림이 도착하는지 확인한다.
5. 성공하면 Scheduling을 `Every 15 minutes`로 설정하고 Scenario를 ON으로 변경한다.

## 11. 최종 모듈 구조
```text
RSS - Watch RSS Feed Items
→ Router
→ Filter
→ Google Sheets - Add a Row
→ Gmail 또는 Slack 알림
```
