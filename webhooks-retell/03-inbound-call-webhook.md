
It’s quite common to want to use different agents under the same number, and quite common to provide context based on caller for a call. For outbound calls, you can do that simply by supplying the call specific information when you call the [create phone call API](https://docs.retellai.com/api-references/create-phone-call). For inbound calls however, you are not the one initiating the call, and you need a way to notify you when that inbound call is received and you can then process it.

This inbound call webhook is designed for this purpose. Once setup, you can override agent id, set dynamic variables and other fields specific to that call, and then you can process the call accordingly. It is part of your number configuration, and it works for numbers that you have purchased or imported.

This feature does not apply for [dial to sip calls](https://docs.retellai.com/deploy/custom-telephony#method-2-dial-to-sip-endpoint) calls, as you can provide call specific information when you register the phone call.

## Webhook Spec

The webhook will `POST` the payload to your endpoint. The webhook has a timeout of 10 seconds. If within 10 seconds no success status (2xx) is received, the webhook will be retried, up to 3 times.

### Request payload

These fields might be provided in the payload depending on your configuration:

- `agent_id`: if the number has inbound agent id set, you will see it in payload
- `from_number`: this will always show up in payload, helps you identify the caller and process the call accordingly
- `to_number`: this will always show up in payload, helps you identify the receiver and process the call accordingly

Note that the call is not connected, and a call object is not yet created (and if you decided not to take the call for example, the call object will not be created). Therefore you will not have a call object and call id inside the payload.

Here’s a sample payload:

```json
{
  "event": "call_inbound",
  "call_inbound": {
    "agent_id": "agent_12345",
    "from_number": "+12137771234",
    "to_number": "+12137771235"
  }
}
```

### Response

We expect a JSON response with a successful status code (2xx) with fields grouped under `call_inbound`. Here’re the allowed fields (all of them are optional):

- `override_agent_id`: if you want to override the agent id, you can set it here
- `dynamic_variables`: if you want to set dynamic variables for this inbound call, you can set it here
- `metadata`: if you want to set metadata for this inbound call, you can set it here

Here’s a sample response:

```json
{
  "call_inbound": {
    "override_agent_id": "agent_12345",
    "dynamic_variables": {
        "customer_name": "John Doe"
    },
    "metadata": {
        "random_id": "12345"
    }
  }
}
```

## FAQ

  
What would happen to the call when the webhook response is not received yet?

The call would continue to stay in ringing state.

What would happen if webhook was not successful?

It would get retried up to 3 times. If all of those attempts fail, it will check whether this number has an inbound agent id set. If it does, it will then try to connect the call to that agent. If not, it will then disconnect the call.

Can I use this webhook to decline inbound calls based on caller number?

Yes you can, if your number does not have an inbound agent id set, and you don’t want to take the call, then you can leave your `call_inbound` object empty. With no agent to handle the call, this call would not be connected.