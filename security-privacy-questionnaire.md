# Self-Review Questionnaire: Security and Privacy

## 1. What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

This feature allows a web site that is not the original Relying Party of a
`PaymentCredential` (which is a public key credential) to exercise the
credential via the Payment Request API to sign over transaction data. This is to
allow a merchant or a Payment Service Provider (PSP) that represents the
merchant to request strong authentication of the payment instrument used in the
transaction without redirecting to the issuing bank of the payment instrument.
Minimizing redirection or any form of friction is critical to avoid cart
abandonment during a checkout process. Direct authentication using the issuing
bank's credential is necessary because the merchant or PSP may be fradulent.

This feature also allows a web site to find out that a given credential does not
exist - the `paymentRequest.show()` call will reject if no matching credential
is found. We believe this does not pose a significant probing risk because
`paymentRequest.show()` consumes a user activation. This makes it hard for a
malicious merchant to automate probing at scale.

## 2. Is this specification exposing the minimum amount of information necessary to power the feature?

Yes.

## 3. How does this specification deal with personal information or personally-identifiable information or information derived thereof?

This feature does not collect or expose any such information.

## 4. How does this specification deal with sensitive information?

The only sensitive information this feature handles is an opaque string that can
be mapped to a payment instrument by an issuing bank. This instrument ID is
saved in the user agent along side the public key credential when the issuing
bank creates a `PaymentCredential`. Later, an origin that has access to this
instrument ID, presumably via a trusted server integration with the issuing
bank, can provide it to the user agent via [Payment Request API] to exercise the
corresponding `PaymentCredential`.

## 5. Does this specification introduce new state for an origin that persists across browsing sessions?

This feature allows an issuing bank to persist an icon, a descriptive string and
an issuer-defined payment instrument ID with a public key credential when it
creates a new `PaymentCredential`. However, this data is only used by the
user agent to render UI to the user, and cannot be queried via JavaScript.

## 6. What information from the underlying platform, e.g. configuration data, is exposed by this specification to an origin?

This feature allows a web site to detect, after consuming a user activation,
that the underlying platform does not have a credential with the credential ID
provided by the website.

## 7. Does this specification allow an origin access to sensors on a user’s device

This feature allows an origin to access the user's authenticator to create or
exercise a public key credential.

## 8. What data does this specification expose to an origin?  Please also document what data is identical to data exposed by other features, in the same or different contexts.

This feature exposes the same data as [Web Authentication] with one exception:
it allows any origin to exercise a credential via Payment Request API, provided
that the user consents on a user agent's native UI that clearly indicates that
the calling origin is requesting payment using a specific payment instrument.
When the user consents and provides an authentication gesture, the calling
origin gains access to a [PublicKeyCredential] that contains an
[AuthenticatorAssertionResponse]. This data structure contains a credential ID,
which is information the caller already has, and a crytographic signature, which
is not useful unless the caller has access to the relevant public key.

## 9. Does this specification enable new script execution/loading mechanisms?

No.

## 10. Does this specification allow an origin to access other devices?

No.

## 11. Does this specification allow an origin some measure of control over a user agent's native UI?

Yes. This feature allows an origin (usually a bank) to associate an icon and a
descriptive string with a public key credential. This icon and string are
displayed to the user in the user agent's native UI when this credential is
exercised via Payment Request API.

## 12. What temporary identifiers might this this specification create or expose to the web?

This specification does not create any temporary identifier beyond what is
created by [Web Authentication].

## 13. How does this specification distinguish between behavior in first-party and third-party contexts?

This feature inherits the behavior from [Payment Request API] and
[Web Authentication], namely:

- A `PaymentCredential` can only be created in a first-party secure context.
- A `PaymentCredential` can be exercised in any context where Payment Request
  API is allowed, i.e. a first-party secure context, or a third-party secure
  context with [delegated permission] from the top-level context.

[delegated permission]: https://w3c.github.io/payment-request/#permissions-policy

## 14. How does this specification work in the context of a user agent’s Private Browsing or "incognito" mode?

This feature behaves identically regardless of Private Browsing or "incognito"
mode. This is identical to [Web Authentication].

## 15. Does this specification have a "Security Considerations" and "Privacy Considerations" section?

Yes.

## 16. Does this specification allow downgrading default security characteristics?

This feature downgrades the Relying Party restriction of [Web Authentication]
when a credential is exercised inside a payment context. The original
restriction was meant to prevent phishing. We believe this feature does not
increase phishing risk because the user agent shows a native UI to clearly
inform the user the parties involved in a payment transaction who are seeking to
authenticate the user.

## 17. What should this questionnaire have asked?

None.

[Payment Request API]: https://w3c.github.io/payment-request
[PublicKeyCredential]: https://www.w3.org/TR/webauthn/#iface-pkcredential
[AuthenticatorAssertionResponse]: https://www.w3.org/TR/webauthn/#authenticatorassertionresponse
[Web Authentication]: https://www.w3.org/TR/webauthn
