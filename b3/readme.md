
# 주식뉴스 자동 수집 및 알림 자동화 프로젝트

## 1. 프로젝트 개요
이 프로젝트는 Google News RSS를 이용해 관심 종목의 주식뉴스를 자동으로 수집하고, 조건에 맞는 뉴스만 Google Sheets에 저장한 뒤 Gmail 또는 Slack으로 알림을 보내는 자동화 워크플로우이다.

## 2. 프로젝트 목표
- 관심 종목 관련 뉴스를 자동 수집한다.
- 종목명 또는 중요 키워드가 포함된 뉴스만 필터링한다.
- 수집된 뉴스를 Google Sheets에 기록한다.
- 중요한 뉴스는 Gmail 또는 Slack으로 알림을 보낸다.

## 3. 사용 도구
| 구분 | 도구 |
|---|---|
| 자동화 도구 | Make, Zapier |
| 뉴스 수집 | Google News RSS |
| 데이터 저장 | Google Sheets |
| 알림 | Gmail, Slack |

## 4. 전체 흐름
```text
Google News RSS
→ 새 뉴스 감지
→ 종목명/키워드 필터링
→ Google Sheets 기록
→ Gmail 또는 Slack 알림
```

## 5. RSS 주소 예시
```text
https://news.google.com/rss/search?q=삼성전자%20주식&hl=ko&gl=KR&ceid=KR:ko
https://news.google.com/rss/search?q=SK하이닉스%20주식&hl=ko&gl=KR&ceid=KR:ko
https://news.google.com/rss/search?q=테슬라%20주식&hl=ko&gl=KR&ceid=KR:ko
```

## 6. Google Sheets 컬럼
| 컬럼명 | 설명 |
|---|---|
| 수집시간 | 자동화 실행 시간 |
| 종목명 | 뉴스 관련 종목 |
| 뉴스제목 | 수집된 뉴스 제목 |
| 키워드 | 필터링된 주요 키워드 |
| 뉴스링크 | 원문 링크 |
| 발행일 | 뉴스 발행 날짜 |
| 알림여부 | 알림 발송 여부 |

## 7. 기대 효과
이 자동화를 사용하면 사용자가 직접 뉴스 사이트를 검색하지 않아도 관심 종목 뉴스를 빠르게 확인할 수 있다. 또한 뉴스가 자동으로 기록되기 때문에 이후 분석이나 보고서 작성에도 활용할 수 있다.

## 8. 주의사항
본 프로젝트는 투자 추천 시스템이 아니라 뉴스 수집 자동화 시스템이다. 따라서 매수, 매도, 수익 보장 등의 표현은 사용하지 않고 참고용 정보 수집 목적으로만 사용한다.

## 9. 구현 가이드
자세한 구현 방법은 아래 문서를 참고한다.

- [Make 구현 가이드](./MAKE_GUIDE.md)
- [Zapier 구현 가이드](./ZAPIER_GUIDE.md)
