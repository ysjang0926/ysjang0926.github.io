---
layout: post
title:  "Airflow 개인 슬랙 채널 알림 설정하기"
subtitle: 
categories: etc
tags: etc_work
comments: true
---

- Airflow에서 DAG 실행 상태를 내 개인 슬랙 채널에서만 받고 싶을 때, 어떻게 설정해야 할까요?
- 이번 글에서는 Slack 개인 채널을 생성하고 Webhook을 연결한 후, Airflow에서 설정하는 과정을 정리해 보았습니다.
- 한눈에 이해할 수 있도록 정리해보았으니, 가볍게 읽어주시길 바랍니다! 🙌
 
---------
Airflow에서 DAG 실행 상태를 내 **개인 슬랙 채널에서만** 받고 싶다면, **Slack Webhook을 직접 설정**하면 됩니다. 하지만 처음 설정하는 분들에게는 Webhook 생성, Airflow와의 연결 과정이 다소 낯설 수 있습니다. <br>
이 글에서는 **Slack 채널 생성 → Webhook 연결 → Airflow 설정 변경** 과정을 단계별로 자세히 설명해 보겠습니다. 초보자도 쉽게 따라할 수 있도록 하나씩 차근히 진행해 볼까요? 😊

## 1. Slack에서 개인 채널 & Webhook 설정하기
### 📌 개인 슬랙 채널 생성하기
1. **Slack에서 새로운 비공개 채널을 생성**합니다. (예: `#airflow-my-alerts`)
2. 비공개 채널이므로 초대받은 사람만 메시지를 볼 수 있습니다.

### 🔗 Slack Webhook 설정하기
Webhook을 설정하면 Airflow가 슬랙 채널로 메시지를 보낼 수 있습니다.

1. **Slack API 페이지 이동** ([Slack API](https://api.slack.com/apps))
2. `Create an App` 클릭 → `From Scratch` 선택 → 앱 이름 입력 → 워크스페이스 선택 → `Create App` 클릭
3. 앱 설정 페이지에서 `Incoming Webhooks` 활성화
4. `Add New Webhook to Workspace` 클릭
5. 방금 만든 개인 채널 `#airflow-my-alerts` 선택
6. Webhook URL이 생성됩니다. 예시:
   ```
   https://hooks.slack.com/services/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
   ```
7. **이 URL을 복사하여 나중에 사용할 수 있도록 저장합니다.**

<br>

## 2. Airflow에서 Webhook 추가하기
이제 Airflow에서 Slack Webhook을 사용할 수 있도록 설정해야 합니다.

### ⚙️ Airflow Connection 설정
1. **Airflow Web UI 접속** (`Admin > Connections` 이동)
2. `Create` 클릭하여 새로운 Connection 추가
3. 아래 값 입력:
   - **Connection ID**: `slack-me-alert` (예시)
   - **Connection Type**: `HTTP`
   - **Host**: Webhook URL (Slack에서 복사한 URL 입력)
4. `Save` 클릭

### 🛠 Airflow Variable 설정
1. **Admin > Variables** 이동
2. `Create` 클릭하여 새로운 Variable 추가
3. 아래 값 입력:
   - **Key**: `slack_conn_id_me`
   - **Value**: `slack-me-alert` (위에서 만든 Connection ID)
4. `Save` 클릭

<br>

## 3. Airflow 코드 수정해서 개인 채널로 알림 받기
이제 Airflow DAG에서 Slack Webhook을 사용하도록 설정하면 됩니다.

```python
from airflow.providers.slack.operators.slack_webhook import SlackWebhookOperator
from airflow.models import Variable

OWNER_SLACK_ID = "U00AB0CDEFG"  # 개인 멤버키
PERSONAL_SLACK_CONN_ID = Variable.get("slack_conn_id_chacha")  # 개인 슬랙 연결 ID

default_args = {
    'owner': "chacha",
    "on_failure_callback": generic_slack_alert_failure(slack_conn_id=PERSONAL_SLACK_CONN_ID, owner=OWNER_SLACK_ID)
}

# DAG 작성
with models.DAG(
    DAG_ID,
    tags=["melon", "daily"], 
    default_args=default_args,  # 추가하기
) as dag:
    
    send_slack_message = SlackWebhookOperator(
        task_id="send_slack_message",
        http_conn_id=PERSONAL_SLACK_CONN_ID,
        message=(
            f"ENV: {ENV}"
            + f"\n Melon - artist_fandom_clustering (<@{OWNER_SLACK_ID}>)"
            + "\n :large_green_circle: Daily job is completed successfully"
        )
    )
    
    artist_fandom_clustering >> send_slack_message
```

### ✅ **코드 설명**
- `PERSONAL_SLACK_CONN_ID`: **Airflow Variable에서 Webhook 연결 ID 가져오기**
- `on_failure_callback`: DAG 실패 시 개인 채널로 알림 받기
- `SlackWebhookOperator`: DAG 완료 시 개인 채널에 메시지 전송

<br>

## 🎯 정리
✔️ **Slack에서 개인 채널을 만들고 Webhook 설정**  
✔️ **Airflow에서 Webhook URL을 Connection & Variable로 등록**  
✔️ **DAG 코드에서 `SlackWebhookOperator`를 사용하여 개인 채널로 알림 보내기**

이제 Airflow에서 실행 상태를 **내 개인 슬랙 채널에서만** 받을 수 있습니다!🚀

