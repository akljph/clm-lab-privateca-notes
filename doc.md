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
