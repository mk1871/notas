
1. **Create your server endpoint**

Set up an HTTP or HTTPS endpoint function that can accept webhook requests with a POST method.

Example endpoint:



Node.js

```typescript
// install the sdk: https://docs.retellai.com/get-started/sdk
import { Retell } from "retell-sdk";
import express, { Request, Response } from "express";

const app = express();
app.use(express.json());

app.post("/webhook", (req: Request, res: Response) => {
  const {event, call} = req.body;
  switch (event) {
    case "call_started":
      console.log("Call started event received", call.call_id);
      break;
    case "call_ended":
      console.log("Call ended event received", call.call_id);
      break;
    case "call_analyzed":
      console.log("Call analyzed event received", call.call_id);
      break;
    default:
      console.log("Received an unknown event:", event);
  }
  // Acknowledge the receipt of the event
  res.status(204).send();
});
```

Python

```python
# Install the SDK: https://docs.retellai.com/get-started/sdk
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from retell import Retell

retell = Retell(api_key=os.environ["RETELL_API_KEY"])

@app.post("/webhook")
async def handle_webhook(request: Request):
    try:
        post_data = await request.json()
        if post_data["event"] == "call_started":
            print("Call started event", post_data["data"]["call_id"])
        elif post_data["event"] == "call_ended":
            print("Call ended event", post_data["data"]["call_id"])
        elif post_data["event"] == "call_analyzed":
            print("Call analyzed event", post_data["data"]["call_id"])
        else:
            print("Unknown event", post_data["event"])
        return JSONResponse(status_code=204)
    except Exception as err:
        print(f"Error in webhook: {err}")
        return JSONResponse(
            status_code=500, content={"message": "Internal Server Error"}
        )
```


1. **Test your endpoint locally**

Before going live, test your application integration locally. For example, host the endpoint on `localhost:8080/webhook` and test with Postman:

![Testing webhook with postman](https://mintlify.s3.us-west-1.amazonaws.com/retellai/images/webhook_postman.png)

Test using this CURL command:

```bash
curl --location 'localhost:8080/webhook' \
--header 'Content-Type: application/json' \
--data '{
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
    "opt_out_sensitive_data_storage": false
  }
}'
```

1. **Make your local endpoint online**

Deploy your endpoint using [Ngrok](https://ngrok.com/docs/getting-started/):

1. Install ngrok:

```bash
brew install ngrok/ngrok/ngrok
```

2. Start ngrok:

```bash
ngrok http http://localhost:8080
```

You’ll see a console UI like this:

```
ngrok                                                                   (Ctrl+C to quit)

Session Status                online
Account                       inconshreveable (Plan: Free)
Version                       3.0.0
Region                        United States (us)
Latency                       78ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://84c5df474.ngrok-free.dev -> http://localhost:8080
```

Your webhook endpoint will be `https://84c5df474.ngrok-free.dev/webhook`

1. **Register your webhook endpoint**

You have two options:

**Option 1: Register an account level webhook** Set up through the dashboard’s webhooks tab for events related to any agent under your account.

![](https://mintlify.s3.us-west-1.amazonaws.com/retellai/images/webhook.png)

**Option 2: Register an agent level webhook** Set up through the dashboard’s agent detail page. Note: If set, account level webhooks will not be triggered for that agent.

![](https://mintlify.s3.us-west-1.amazonaws.com/retellai/images/agent_webhook.png)

1. **Verify your webhook endpoint**

Start a web call in the dashboard to verify the webhook is triggered correctly.

