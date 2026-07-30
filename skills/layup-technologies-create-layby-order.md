---
name: Create and collect a LayUp lay-by order
description: Create a lay-by order for a customer, preview the payment plan, send the customer their payment request, and record payments as they arrive.
api: openapi/layup-technologies-openapi-original.json
operations: [PaymentPlanService_CreatePreview, OrderService_Create, OrderService_SendOrderRequest, OrderService_Get, PaymentService_Create]
---

# Create and collect a LayUp lay-by order

Use this to onboard a customer onto a lay-by (Save Now, Buy Later) plan on LayUp.

## Auth
- All requests go to `https://api.layup.co.za` (or `https://sandbox-api.layup.co.za` to test).
- Send your API key in the `apikey` HTTP header on every request.
- Bodies and responses are `application/json`.

## Steps
1. **Preview the plan.** Call `PaymentPlanService_CreatePreview` (POST `/v1/payment-plan/preview`) with the order amount, deposit and schedule (weekly/monthly) to show the customer their instalment breakdown before committing.
2. **Create the order.** Call `OrderService_Create` (POST `/v1/orders`) with the merchant, customer cell number, line items and the deposit/schedule parameters. This places the order in `PLACED` state and holds inventory.
3. **Send the payment request.** Call `OrderService_SendOrderRequest` (POST `/v1/orders-send-request`) to email/SMS the customer a payment link.
4. **Track state.** Call `OrderService_Get` (GET `/v1/orders/{_id}`) to read the order state (`PLACED` → `PARTIAL` → `COMPLETED`, or `EXPIRED`/`CANCELLED`).
5. **Record offline/manual payments if needed.** Call `PaymentService_Create` (POST `/v1/payments`) to attach a payment to the order.

## Rules
- Errors are plain `application/json` with HTTP 400 (validation), 403 (bad/missing apikey), 404 (not found), 500. See `errors/layup-technologies-problem-types.yml`.
- There is no idempotency-key mechanism — do not blindly retry `OrderService_Create` or `PaymentService_Create`; re-`GET` first.
- Subscribe to order and payment webhooks (see `asyncapi/layup-technologies-webhooks.yml`) instead of polling; they originate from IP `99.81.244.160`.
