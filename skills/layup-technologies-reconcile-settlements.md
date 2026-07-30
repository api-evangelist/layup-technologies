---
name: Reconcile LayUp merchant settlements
description: Find merchants due for settlement, pull the settlement report, and download the settlement file for reconciliation.
api: openapi/layup-technologies-openapi-original.json
operations: [MerchantService_getMerchantsToSettle, SettlementService_Create, SettlementService_GetReport, SettlementService_DownloadReport]
---

# Reconcile LayUp merchant settlements

Use this to reconcile funds LayUp settles to a merchant once lay-by orders complete.

## Auth
- Base URL `https://api.layup.co.za`; send the API key in the `apikey` header.

## Steps
1. **List merchants to settle.** Call `MerchantService_getMerchantsToSettle` (GET `/v1/merchants-to-settle`).
2. **Create the settlement (if driving it).** Call `SettlementService_Create` (POST `/v2/settlements`) for the merchant.
3. **Pull the report.** Call `SettlementService_GetReport` (GET `/v2/settlements-report`) for the settlement summary.
4. **Download the file.** Call `SettlementService_DownloadReport` (GET `/v3/settlement-reports/{merchantId}/{settlementReferenceWithExtension}`) to fetch the downloadable settlement report for reconciliation.

## Rules
- Note the mixed versions: settlement operations live under `/v2` and `/v3` while most of the API is `/v1`.
- Errors are plain JSON with standard HTTP status codes (see `errors/layup-technologies-problem-types.yml`).
