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

## Important notes on behavior:

- Do not use **--allow-any-name**. It would defeat the hostname restriction established by **--allowed-domains**.
- **--allow-subdomains** is not required because this issuer permits only one exact hostname.
- **--destination-path** is required for stored-certificate lifecycle features such as auto-renewal and CRL management.
- Renewal is scheduled 30 days before the certificate's actual expiration date. The 30-, 7-, and 1-day expiration events provide operational visibility. After a successful automatic renewal, the new certificate version receives a new expiration date.
- Client-authentication and code-signing flags are intentionally excluded.
- **--allowed-ip-sans** and **--allowed-uri-sans** are omitted because this server needs only a DNS SAN.
- The 90-day lifetime is intentionally much shorter than the CA lifetime. Do not repeat **--ttl**, and do not request a leaf lifetime equal to the CA lifetime.
