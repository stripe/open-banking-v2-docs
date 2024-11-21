# AISP API Overview

## Base URL
The base URL for all AIS APIs is: `https://rs1.api-v1-uk.stripeopenbanking.com/open-banking/v3.1/aisp/**`

## Account Access Consents
[Account Access Consents API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Account_Access)

## Accounts
[Accounts API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Accounts)

Stripe members will have one of the following accounts:
- Stripe Payment Account
- Stripe Issuing Account

## Balances
[Balances API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Balances)

Balances shown in this endpoint provide the `InterimAvailable` value.

`InterimAvailable` balance is the value displayed most widely to our members within the Stripe apps.

## Transactions
[Transactions API](/perry/developer/documentation?resource=ukhub-stripe-portal&document=swagger/account-info-openapi.yaml#operations-tag-Transactions)

Transactions are categorized using the `ProprietaryBankTransactionCode` field. A human readable description will be provided in the `TransactionInformation` field when applicable.

