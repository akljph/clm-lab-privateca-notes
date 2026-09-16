# Command: Create PKI Issuer

```bash
akeyless create-pki-cert-issuer \
  --name '[[ Instruqt-Var key="CLM_ISSUER_NAME" hostname="track-host" ]]' \
  --signer-key-name '[[ Instruqt-Var key="CLM_SIGNER_KEY_NAME" hostname="track-host" ]]' \
  --ttl 90d \
  --allowed-domains '[[ Instruqt-Var key="CLM_CERTIFICATE_HOSTNAME" hostname="track-host" ]]' \
  --server-flag \
  --key-usage DigitalSignature,KeyEncipherment \
  --critical-key-usage true \
  --basic-constraints 'critical,CA:false' \
  --organizations 'Akeyless Training' \
  --organization-units 'CLM Lab' \
  --country US \
  --destination-path [[ Instruqt-Var key="CLM_BASE_PATH" hostname="track-host" ]]/Certificates \
  --gw-cluster-url "[[ Instruqt-Var key="CLM_GATEWAY_URL" hostname="track-host" ]]" \
  --create-private-crl \
  --create-private-ocsp \
  --ocsp-ttl 12h \
  --auto-renew \
  --scheduled-renew 30 \
  --expiration-event-in 30 \
  --expiration-event-in 7 \
  --expiration-event-in 1 \
  --description 'Restricted Nginx TLS issuer for the CLM training lab' \
  --uid-token '[[ Instruqt-Var key="UID_TOKEN" hostname="track-host" ]]'
```

## Important notes on behavior:

- Do not use **--allow-any-name**. It would defeat the hostname restriction established by **--allowed-domains**.
- **--allow-subdomains** is not required because this issuer permits only one exact hostname.
- **--destination-path** is required for stored-certificate lifecycle features such as auto-renewal and CRL management.
- Renewal is scheduled 30 days before the certificate's actual expiration date. The 30-, 7-, and 1-day expiration events provide operational visibility. After a successful automatic renewal, the new certificate version receives a new expiration date.
- Client-authentication and code-signing flags are intentionally excluded.
- **--allowed-ip-sans** and **--allowed-uri-sans** are omitted because this server needs only a DNS SAN.
- The 90-day lifetime is intentionally much shorter than the CA lifetime. Do not repeat **--ttl**, and do not request a leaf lifetime equal to the CA lifetime.

---

# Command: Get PKI Certificate

```bash
akeyless get-pki-certificate \
  --cert-issuer-name '[[ Instruqt-Var key="CLM_ISSUER_NAME" hostname="track-host" ]]' \
  --csr-file-path /root/nginx.csr \
  --key-file-path /root/nginx.key \
  --outfile /root/nginx.crt \
  --uid-token '[[ Instruqt-Var key="UID_TOKEN" hostname="track-host" ]]' ; echo
```

## Important notes on behavior:

- Do not pass **--common-name** or **--alt-names**; when a CSR is supplied, those values are taken from the CSR.
- The certificate TTL is also intentionally omitted. The issuer's 90-day default is used, avoiding a request that equals or exceeds the issuer maximum.
- **--key-file-path** is important in this workflow: When a CSR and matching private key are supplied, the private key is stored securely with the generated certificate item. Without a stored private key, Akeyless cannot provision the server key *nginx.key* or reuse that key during renewal.

---

# How certificate revocation works
When a Certificate Authority revokes a certificate, it does not modify or delete the certificate file installed on the server. Instead, the CA records the certificate’s serial number as revoked and publishes this status through a Certificate Revocation List (CRL), the Online Certificate Status Protocol (OCSP), or both.

Therefore, a revoked certificate may remain on the server, and the server may continue presenting it during TLS connections. Viewing the PEM file, checking its expiration date, or successfully connecting to the server does not prove that the certificate has not been revoked.

To determine whether a certificate is revoked, a client or administrator must:

Identify the CA that issued the certificate.
Locate the CRL Distribution Point or OCSP URL embedded in the certificate.
Download the current CRL or query the OCSP responder.
Check whether the certificate’s serial number is listed as revoked.

The same validation can be performed before installing a certificate on a server. In production environments the clients, load balancers, security scanners, or certificate-management processes must be configured to enforce CRL or OCSP validation. If no revocation check is performed, a revoked certificate may continue to appear usable until it expires.
