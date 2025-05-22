Webhooks allow your application to receive real-time notifications about events that occur in your Retell AI account. Instead of continuously polling our API, webhooks push data to your application as events happen, making your integrations more efficient and responsive.

## Event Types

Retell AI supports the following webhook events:

> [!WARNING]  
> If the call did not connect (like dial failed), the `call_started` webhook event will not be triggered.

| Event Type      | Description                                                        | Payload                                                                                                      |
| --------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| `call_started`  | Triggered when a new call begins                                   | Basic call information                                                                                       |
| `call_ended`    | Triggered when a call completes, transfers, or encounters an error | all fields from the [call object](https://docs.retellai.com/api-references/get-call) except `call_analysis`. |
| `call_analyzed` | Triggered when call analysis is complete                           | Full call data including `call_analysis` object                                                              |

## Common Use Cases

1. **Real-time Analytics**
    
    - Track call statistics and performance metrics
    - Monitor call volumes and patterns
    - Trigger alerts for specific call outcomes
2. **System Integration**
    
    - Update CRM records when calls complete
    - Trigger workflow automations based on call analysis
    - Archive call transcripts in your data warehouse
3. **Call Monitoring**
    
    - Get notified of failed or transferred calls
    - Track call duration and completion status
    - Monitor agent performance in real-time

## Webhook Spec

The webhook will `POST` the payload to your endpoint. The webhook has a timeout of 10 seconds. If within 10 seconds no success status (2xx) is received, the webhook will be retried, up to 3 times.

The webhook will be triggered in order, but is not blocking. For example, if the webhook for `call_started` is not successful, we can still trigger `call_ended` webhook.

When the call did not connect (like calls with `dial_failed`, `dial_no_answer`, `dial_busy` disconnection reason), it will not have its `call_started` webhook triggered. It will still have its `call_ended` and `call_analyzed` webhook triggered.

### Request payload

The webhook will contain the event type and the call object. Here’s a sample payload:

```json
{
  "event": "call_ended",
  "call": {
    "call_type": "phone_call",
    "from_number": "+12137771234",
    "to_number": "+12137771235",
    "direction": "inbound",
    "call_id": "Jabr9TXYYJHfvl6Syypi88rdAHYHmcq6",
    "agent_id": "oBeDLoLOeuAbiuaMFXRtDOLriTJ5tSxD",
    "call_status": "registered",
    "metadata": {},
    "retell_llm_dynamic_variables": {
      "customer_name": "John Doe"
    },
    "start_timestamp": 1714608475945,
    "end_timestamp": 1714608491736,
    "disconnection_reason": "user_hangup",
    "transcript": "...",
    "transcript_object": [ [Object], [Object], [Object], [Object] ],
    "transcript_with_tool_calls": [ [Object], [Object], [Object], [Object] ],
    "opt_out_sensitive_data_storage": false
  }
}
```

### Response

We expect a successful status code (2xx) to be returned. No body is expected.



