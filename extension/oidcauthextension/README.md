# Authenticator - OIDC

This extension implements a `configauth.ServerAuthenticator`, to be used in receivers inside the `auth` settings. The authenticator type has to be set to `oidc`.

## Configuration

```yaml
extensions:
  oidc:
    issuer_url: http://localhost:8080/auth/realms/opentelemetry
    issuer_ca_path: /etc/pki/tls/cert.pem
    audience: account
    username_claim: email

receivers:
  otlp:
    protocols:
      grpc:
        auth:
          authenticator: oidc

processors:

exporters:
  logging:
    logLevel: debug

service:
  extensions: [oidc]
  pipelines:
    traces:
      receivers: [otlp]
      processors: []
      exporters: [logging]
```

Firebase AppCheck is an example service that both uses custom headers and doesn't provide an auto-discovery mechanism:

```yaml
extensions:
  oidc:
    issuer_url: https://firebaseappcheck.googleapis.com/<PROJECT_NUMBER>
    jwks_url: https://firebaseappcheck.googleapis.com/v1/jwks
    attribute: X-Firebase-AppCheck
    audience: projects/<PROJECT_NUMBER>
...
```