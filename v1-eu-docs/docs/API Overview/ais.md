# AISP API Overview

## Base URL
The base URL for all AIS APIs is: `https://rs1.api-v1-eu.stripeopenbanking.com/open-banking/v3.1/aisp/**`

## Account Access Consents
[Account Access Consents API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Account_Access)

## Accounts
[Accounts API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Accounts)

Stripe members will have one of the following accounts:
- Stripe Financial Account

## Balances
[Balances API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Balances)

Balances shown in this endpoint provide the `InterimAvailable` value.

`InterimAvailable` balance is the value displayed most widely to our members within the Stripe apps.

## Transactions
[Transactions API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Transactions)

Pagination is supported on GET /transactions end point with a page size of 100 transactions.

Please note GET /accounts/{AccountId}/transactions end point is not supported
