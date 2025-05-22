
You can use the x-retell-signature header together with your Retell API Key to verify the webhook comes from Retell AI, not from a malicious third party. We have provided verify function in our SDKs to help you with this.

You can also check and allowlist Retell IP addresses: 100.20.5.228.

The following code snippets demonstrate how to verify and handle the webhook in Node.js and Python.

### Install the SDK

Install the cooresponding Python or Node.js SDK:

- [Node.js](https://docs.retellai.com/get-started/sdk)
- [Python](https://docs.retellai.com/get-started/sdk)

Node.js

```TypeScript
// install the sdk: https://docs.retellai.com/get-started/sdk
import { Retell } from "retell-sdk";
import express, { Request, Response } from "express";

const app = express();
app.use(express.json());

app.post("/webhook", (req: Request, res: Response) => {
  if (
    !Retell.verify(
      JSON.stringify(req.body),
      process.env.RETELL_API_KEY,
      req.headers["x-retell-signature"] as string,
    )
  ) {
    console.error("Invalid signature");
    return;
  }
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
        valid_signature = retell.verify(
            json.dumps(post_data, separators=(",", ":"), ensure_ascii=False),
            api_key=str(os.environ["RETELL_API_KEY"]),
            signature=str(request.headers.get("X-Retell-Signature")),
        )
        if not valid_signature:
            print(
                "Received Unauthorized",
                post_data["event"],
                post_data["data"]["call_id"],
            )
            return JSONResponse(status_code=401, content={"message": "Unauthorized"})
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

