# PISP API Overview

## Base URL
The base URL for all PIS APIs is: `https://rs1.api.stripeopenbanking.com/open-banking/v3.1/pisp/**`

## Supported Payment Types
The Stripe API currently only supports:
- Domestic Payments
- International Payments

The Stripe API __does not__ support:

- File & Bulk payments
- `payment-details` end-points
- Domestic Scheduled Payments
- Domestic Standing Orders

The payment request from TPP must have the creditor information.

## The following apply to all payment consents (domestic and international):

### `InstructedAmount/Amount`

- There is no MAX `InstructedAmount/Amount` mandated by Stripe API.
- Stripe suggest PISP notify the PSU that the same limits apply as in their Stripe app. It is possible from time to time that the payment consent is authorised, but the payment initiation fails due to account limits.

### `RemittanceInformation/Reference`
`RemittanceInformation/Reference` is a mandatory field and must adhere to the following:
- Valid characters:
  - A-Z
  - a-z
  - 0-9
  - "space"
  - The characters "&", "-", ".", "/"
- Contiguous characters – user enters 6 or more valid characters but without contiguous string of at least 6 alphanumeric characters
- Must contain a contiguous string of at least 6 alphanumeric characters
- Homogeneous string – user enters 6 or more valid characters (including valid non-alphanumeric characters) - After stripping out non-alphanumeric characters the resulting string cannot consist of all the same character

The PISP may also opt to populate reference field on behalf of the PSU

## The following apply to all domestic payment consents:

### `CreditorAccount` requirements

`CreditorAccount` supports either `UK.OBIE.SortCodeAccountNumber` or `UK.OBIE.Wallet`  for the `Account.SchemeName` parameter. Providing any other value will return an error.

-  `UK.OBIE.Wallet`
   -  `CreditorAccount.Identification`: Must be the recipient account ID (e.g. `acct_test_123`)
   -  `SupplementaryData.CreditorAccount.Destination`: Optional if the recipient has a default destination, otherwise the destination ID (e.g. `usba_test_123`)
-  `UK.OBIE.SortCodeAccountNumber`
   -  `CreditorAccount.Identification`: Must be the concatenated sort code and account number, totaling 14 digits
   -  `CreditorAccount.Name`: Must be the full name (first & last) of the recipient or the name of the business
   -  `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address

### `SupplementaryData` requirements

This is not required if you are using the `UK.OBIE.Wallet` scheme. Stripe's Open Banking API requires you to send additional data in the SupplementaryData field to ensure a successful payment:

-  `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address
-  `SupplementaryData.CreditorAccount.EntityType`: Must be either `individual` or `company` depending on the type of the recipient

### Note on sending a domestic payment from a different currency

If you choose to send a domestic payment from a supported currency on your Financial Account other than `GBP`, please use the international payments API instead and enter the `DestinationCountryCode` as `GB`. You may then specify the `CurrencyOfTransfer` as mentioned below. You may use the `UK.OBIE.SortCodeAccountNumber` scheme for the CreditorAccount. Note that the payment will still land in GBP in the user's bank account.

## The following apply to all international payment consents:

### `CreditorAccount` requirements

`CreditorAccount` supports only `UK.OBIE.IBAN` for the `Account.SchemeName` parameter. Providing any other value will return an error.

-  `CreditorAccount.Identification`: Must be the IBAN of the recipient
-  `CreditorAccount.Name`: Must be the full name (first & last) of the recipient or the name of the business
 
### `CurrencyOfTransfer` requirements

`CurrencyOfTransfer` should always be a supported currency on the user's Stripe Financial Account. This is the currency in which Stripe will debit the user's balance. Users must hold money in that currency at Stripe to be able to send a payment. You may use the AIS APIs to list the user's balances.

### `DestinationCountryCode` requirements

`DestinationCountryCode` should represent the country code of the country where the recipient is based. Currently, Stripe only accepts `FR`. Stripe's Open Banking API supports international payments to France.

### `InstructedAmount/Currency` requirements

`InstructedAmount/Currency` can be any currency. The user can specify the instructed amount in any currency and Stripe will do the required FX to extract money from the user's Stripe account and send the money in the recipient's destination currency (see note below).

### `SupplementaryData` requirements

Stripe's Open Banking API requires you to send additional data in the SupplementaryData field to ensure a successful payment:

-  `SupplementaryData.CreditorAccount.Email`: Must contain the recipient's email address
-  `SupplementaryData.CreditorAccount.EntityType`: Must be either `individual` or `company` depending on the type of the recipient

### Note on recipient destination currency

Stripe only supports payments that land in the recipient's local currency. For instance, when sending a payment to a France based recipient, the payment will post to the user's bank account in Euros.

## Additional required information for KYC purposes

Apart from `SupplementaryData` requirements mentioned above, Stripe requires additional KYC information for the recipient depending on where they are based. Please ensure you send the following fields:

|Recipient Country |Fields to Populate |
|--|--|
|France |If `SupplementaryData.CreditorAccount.EntityType` is `individual`, send `SupplementaryData.CreditorAccount.Individual.DateOfBirth`: The date of birth of the recipient in "YYYY-MM-DD" format |

## Payment dates
Payments can be made on all days including Saturdays, Sundays and Bank Holidays