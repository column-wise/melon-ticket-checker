# 🎫 melon-ticket-checker

멜론티켓 취소표를 주기적으로 확인하여 Slack으로 알림을 보내주는 스크립트

## 동작 방식

1. 20~40초 랜덤 간격으로 멜론티켓 API를 폴링
2. 취소표 발생 시 Slack 알림 전송
3. 세션 만료 감지 시 쿠키 갱신 알림 전송

## 설치

```bash
pip install requests
```

## 설정

### 1. slack_config.json

`slack_config.example.json`을 복사해서 `slack_config.json`으로 이름 변경 후 Webhook URL 입력

```json
{
  "webhook_url": "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"
}
```

Slack Webhook URL 발급: [Slack API - Incoming Webhooks](https://api.slack.com/messaging/webhooks)

### 2. target.json

`target.example.json`을 복사해서 `target.json`으로 이름 변경 후 공연 정보와 쿠키 입력

```json
{
  "prodId": "212811",
  "scheduleNo": "100003",
  "seatId": "5_0",
  "volume": "1",
  "selectedGradeVolume": "1",
  "cookies": {
    "PCID": "...",
    "JSESSIONID": "...",
    "keyCookie": "...",
    "TKT_POC_ID": "...",
    "NetFunnel_ID": "..."
  }
}
```

**공연 정보 확인 방법**
1. 멜론티켓 로그인 후 예매 페이지로 이동
2. 개발자도구(F12) → Network → `seatStateInfo.json` 요청 클릭
3. Payload 탭에서 `prodId`, `scheduleNo`, `seatId` 값 확인

**쿠키 확인 방법**
1. 개발자도구(F12) → Application → Cookies → `ticket.melon.com`
2. 위 항목 값 복사해서 붙여넣기

> ⚠️ 세션 쿠키는 일정 시간 후 만료됩니다. 만료 시 Slack으로 갱신 알림이 옵니다.

## 실행

```bash
python checker.py
```

## 알림 종류

| 알림 | 내용 |
|---|---|
| 🎫 취소표 발생 | `rmdSeatCnt > 0` — 예매 가능 매수 표시 |
| ⚡ 상태 변화 감지 | `chkResult > 0` — 상태 변화 가능성 |
| ⚠️ 세션 만료 | target.json의 cookies 갱신 필요 |
| ❌ 연속 오류 | 5회 이상 API 호출 실패 |
