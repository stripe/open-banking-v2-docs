# FAQs

## General

### What is Open Banking?

Open Banking is a reform, called for by the Competitions & Market Authority (CMA), which mandates Stripe and the eight other largest current account providers (CMA9) to securely share customer account data and initiate payments with registered third party providers (TPPs) provided the customer has given their consent.

Please find more information at [Open Banking](https://www.openbanking.org.uk/)


### What are our Open Banking APIs?
#### Open Data
These Open Data APIs allow API providers (e.g. banks, building societies and ATM providers) to develop API endpoints for products, branches and ATMs data which can then be accessed by anyone.

#### Account & Transactions
These read/write APIs provide the ability for approved/authorised account information service providers (AISPs) to access a customer’s (payment service user, PSU) account and transaction information for domestic business current accounts (BCAs) and personal current accounts (PCAs), only when the PSU grants consent. This API is developed according to the Open Banking Read/Write API Specifications, see [https://www.openbanking.org.uk/](https://www.openbanking.org.uk/)

### Payment Initiation

These read/write APIs provide the ability for authorised payment initiation service providers (PISPs) to initiate domestic payments, setup new domestic scheduled payments & domestic standing orders, only when the PSU grants consent. This API is developed according to the Open Banking Read/Write API Specifications, see [https://www.openbanking.org.uk/](https://www.openbanking.org.uk/)

#### Confirmation of Funds

Ths read/write API allow a Card Based Payment Instrument Issuer ('CBPII') to make a request to confirm funds are available. This API is developed according to the Open Banking Read/Write API Specifications, see [https://www.openbanking.org.uk/](https://www.openbanking.org.uk/)


### What are the roles a TPP can perform?

A TPP, Third Party Provider, can perform the following roles once they are registered with their National Competent Authority (NCA):

Account Information Service Provider (AISP)

Payment Initiation Service Provider (PISP)

Technical Service Provider (TSP)

Card Based Payment Instrument Issuer (CBPII)



## Read/Write APIs


### How can I access Stripe Read/Write APIs?
As a TPP, in order to access our Read/Write APIs, you need to be enrolled with Open Banking (Enrolling Onto Open Banking Guide) and registered with the Financial Conduct Authority (FCA) or a National Competent Authority (NCA), as either an AISP and/or PISP, TSP or CBPII.

This will then enable you to access our APIs through the Stripe Developer Portal

### As a Third Party Provider, is there somewhere I can test prototype Open Banking Solutions?
 Yes, Stripe has a test facility [../40-sandbox.md](Sandbox) available through our Developer Portal. This will be made available in March 2019.

Check out our [../20-getting-started.md](Get Started) guide for a step by step guide on how to start testing with our Sandbox APIs.


### Where are the specifications you have used to build your current APIs?
There are full specifications provided by OBIE available on their [https://openbanking.atlassian.net/wiki/spaces/DZ/overview](Developer Zone) from which we’ve built our APIs.

## Response Codes

### I am getting a 401 unauthorized response when invoking /token endpoints
(1) Make sure you have registered your SSA in Stripe Developer Portal and the subscription of the Accounts Service Provider API and/or Payments Service Provider API is approved by Stripe

(2) Make sure you are following client_secret_post for the OIDC calls

(3) Make sure you are sending client_id & client_secret as part of x-www-form-urlencoded body parameter

### I am getting an SSL Handshake Error when trying to invoke /token or resource endpoints.
Check that you are using the correct network certificate signed by Open Banking to establish the TLS MA connection