---
layout: post
title:  "Airflow에서 슬랙 개인 알림 받기, 쉽고 깔끔하게!"
subtitle: 
categories: etc
tags: etc_work
comments: true
---

- Airflow에서 DAG 실행 상태를 내 개인 슬랙 채널에서만 받고 싶을 때, 어떻게 설정해야 할까요?
- 이번 글에서는 Slack 개인 채널을 생성하고 Webhook을 연결한 후, Airflow에서 설정하는 과정을 정리해 보았습니다.
 
---------
Airflow에서 DAG 실행 상태를 내 **개인 슬랙 채널에서만** 받고 싶다면, **Slack Webhook을 직접 설정**하면 됩니다. 하지만 저처럼 처음 설정하는 분들에게는 Webhook 생성, Airflow와의 연결 과정이 다소 낯설 수 있습니다. <br>

이 글에서는 **Slack 채널 생성 → Webhook 연결 → Airflow 설정 변경** 과정을 단계별로 자세히 설명해 보겠습니다. 초보자도 쉽게 따라할 수 있도록 하나씩 차근히 진행해 볼까요? 😊

<br>

## 1️⃣ Slack에서 개인 채널 & Webhook 설정하기
### 📌 개인 슬랙 채널 생성하기
1. **Slack에서 새로운 비공개 채널을 생성**합니다. (예: `#airflow-my-alerts`)
2. 비공개 채널이므로 초대받은 사람만 메시지를 볼 수 있습니다.

### 🔗 Slack Webhook 설정하기
Webhook을 설정하면 Airflow가 슬랙 채널로 메시지를 보낼 수 있습니다.

> 👩🏻‍💻 **Webhook**이란? <br>
> Webhook은 서버 간에 데이터를 전달하는 간단한 방법입니다. <br>
> Slack Webhook을 사용하면 Airflow에서 특정 이벤트가 발생할 때, 자동으로 Slack으로 메시지를 보낼 수 있습니다. <br>
> 즉, Airflow가 DAG 실행 결과를 Slack에 알릴 수 있도록 Webhook을 설정하는 것입니다.

1. **Slack API 페이지 이동** ([Slack API](https://api.slack.com/apps))
2. `Create an App` 클릭 → `From Scratch` 선택 → 앱 이름 입력 → 워크스페이스 선택 → `Create App` 클릭
3. 앱 설정 페이지에서 `Incoming Webhooks` 활성화
4. `Add New Webhook to Workspace` 클릭
5. 방금 만든 개인 채널 `#airflow-my-alerts` 선택 (예시)
6. Webhook URL이 생성됩니다. (예시)
   ```
   https://hooks.slack.com/services/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
   ```
8. **이 URL을 복사하여 나중에 사용할 수 있도록 저장합니다.**

<br>

## 2️⃣ Airflow에서 Webhook 추가하기
이제 Airflow에서 Slack Webhook을 사용할 수 있도록 설정해야 합니다.

### ⚙️ Airflow Connection 설정
1. **Airflow Web UI 접속** (`Admin > Connections` 이동)
2. `Create` 클릭하여 새로운 Connection 추가
3. 아래 값 입력
   - **Connection ID**: `slack-me-alert` (예시)
   - **Connection Type**: `HTTP`
   - **Host**: Webhook URL (Slack에서 복사한 URL 입력)
4. `Save` 클릭

### 🛠 Airflow Variable 설정
1. **Admin > Variables** 이동
2. `Create` 클릭하여 새로운 Variable 추가
3. 아래 값 입력:
   - **Key**: `slack_conn_id_me` (예시)
   - **Value**: `slack-me-alert` (위에서 만든 Connection ID)
4. `Save` 클릭

<br>

## 3️⃣ Airflow 코드 수정해서 개인 채널로 알림 받기
이제 Airflow DAG에서 Slack Webhook을 사용하도록 설정하면 됩니다.

### 🔹 SlackWebhookOperator란?
SlackWebhookOperator는 Airflow에서 Slack으로 메시지를 전송하는 연산자(Operator)입니다. **Slack의 Incoming Webhook API**를 사용하여 메시지를 보낼 수 있으며, DAG의 실행 상태를 Slack으로 알리는 역할을 합니다. <br>
이를 활용하면 배치 작업 완료, 오류 발생, 경고 등의 이벤트를 Slack에 자동으로 공유할 수 있어 실시간 모니터링과 작업 상태 확인이 훨씬 편리해집니다.


### 🔹 주요 코드 설명
```python
from airflow.providers.slack.operators.slack_webhook import SlackWebhookOperator
from airflow.models import Variable

# Slack에서 받은 사용자 ID (예: U00AB0CDEFG)
OWNER_SLACK_ID = "U00AB0CDEFG"  # 개인 멤버키

# Airflow Variables에서 저장한 Slack Webhook Connection ID 가져오기
PERSONAL_SLACK_CONN_ID = Variable.get("slack_conn_id_me")

# DAG의 기본 설정값을 정의
default_args = {
    'owner': "me",  # DAG 소유자
    "on_failure_callback": generic_slack_alert_failure(slack_conn_id=PERSONAL_SLACK_CONN_ID,
                                                       owner=OWNER_SLACK_ID)  # DAG 실행 실패 시 슬랙 알림 전송
}
```

```python
# DAG 작성 및 Slack 메시지 전송 작업 추가
with models.DAG(
    DAG_ID,
    tags=["daily"], 
    default_args=default_args,  # 위에서 설정한 기본 인자를 적용
) as dag:
    
    # DAG 실행이 완료되었음을 알리는 Slack 메시지 전송 태스크
    send_slack_message = SlackWebhookOperator(
        task_id="send_slack_message",  # 태스크 ID
        http_conn_id=PERSONAL_SLACK_CONN_ID,  # Slack Webhook 연결 정보
        message=(
            f"\n My Job - Model Training Job (<@{OWNER_SLACK_ID}>)"  # DAG 이름과 Slack 사용자 ID 포함
            + "\n :large_green_circle: Daily job is completed successfully"  # 성공 메시지
        )
    )
    
    # DAG 실행 후 Slack 메시지를 보내도록 설정
    model_training >> send_slack_message
```

### ✅ **코드 설명**
- `OWNER_SLACK_ID`: Slack에서 개인 멤버 키를 가져와 메시지에 포함
- `PERSONAL_SLACK_CONN_ID`: Airflow Variables에서 **Slack Webhook Connection ID**를 불러와 사용
- `default_args`: DAG의 기본 실행 설정 (소유자 및 실패 시 알림 처리)
- `SlackWebhookOperator`: DAG 실행이 완료되었을 때 Slack 메시지를 전송하는 역할
  - `task_id`: Airflow에서 태스크를 식별하는 ID
  - `http_conn_id`: 미리 설정한 Slack Webhook Connection ID 사용
  - `message`: Slack에 전송할 메시지 내용
- `artist_fandom_clustering >> send_slack_message`: DAG 실행 후 Slack 메시지 태스크를 실행하도록 설정 (의존성 설정)

<br>

## 🎯 정리
✔️ **Slack에서 개인 채널을 만들고 Webhook 설정**  
✔️ **Airflow에서 Webhook URL을 Connection & Variable로 등록**  
✔️ **DAG 코드에서 `SlackWebhookOperator`를 사용하여 개인 채널로 알림 보내기**

이제 Airflow에서 실행 상태를 **내 개인 슬랙 채널에서만** 받을 수 있습니다!🚀





