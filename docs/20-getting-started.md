# Getting started

## Dynamic Client Registration

Our API allows dynamic client registration in order to create a valid client that is able to use our Authorisation Server. We only trust Software Statement Assertions (SSAs) issued by the Open Banking Directory provided by OBIE. eIDAS certificates are supported via onboarding to the Open Banking Directory (as discussed in more detail below).

The url of the registration endpoint is advertised on our OIDC Discovery Endpoints using the registration_endpoint claim. The aud claim used in the outer JWT of a Dynamic Client Registration request is the OBIE issued org_id (as documented in the OBIE DCR v3.1 standard).

## Production security profile

As defined further in the Stripe Open Banking API Specification

- ID Token Signing Algorithm: `PS256`
- Response Types: `code id_token`
- Request Object Signing Algorithms: `PS256`
- Token Endpoint Auth Singing Algorithms: `PS256`
- Token Endpoint Auth Methods: `private_key_jwt`, `tls_client_auth`

> For private_key_jwt - the `aud` claim is the url of the token endpoint as specified in OIDC client authentication
> The request object used in OIDC flows the aud claim is the issuer url from the Stripe ASPSP .wellknown endpoint (linked below).

> Note: Our Sandbox API also offers less strict profiles to assist with integration testing. See below for more details.

## Certificate Support

### OB Transport & OB Signing Certificates

We recommend that TPPs use OB Transport and OB Signing certificates where possible. TPPs can use their QWACs and QSeals to onboard to the OBIE directory and generate these certificates.

### OB WAC & OB Seal

We support the use of OBWAC and OBSeal on our production and sandbox environments. TPPs can use their QWACs and QSeals to onboard to the OBIE directory and generate these certificates.

### QWACs

We support the use of QWACs, but this is not our recommended approach. TPPs facing issues onboarding with QWACs should contact our support desk. Please attach a pem file of the certificate to your support ticket.

### QSeal

We support the use of QSeals that have been attached to your software statement in the OB Directory.
