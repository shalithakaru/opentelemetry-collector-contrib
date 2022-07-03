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

Firebase AppCheck is an example service that uses custom headers, doesn't provide an auto-discovery mechanism and has an `issuer_url` that isn't simply the base URL of the JWKS URL.

```yaml
extensions:
  oidc:
    issuer_url: https://firebaseappcheck.googleapis.com/
    jwks_url: https://firebaseappcheck.googleapis.com/v1/jwks
    attribute: X-Firebase-AppCheck
    audience: projects/<PROJECT_NUMBER>
...

```

Github provides an OIDC server that can allow Github Workflows to authenticate to external services. The JWT provided contains a [number of useful fields](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#understanding-the-oidc-token) to help restrict the audience.

```yaml
extensions:
  oidc:
    issuer_url: https://token.actions.githubusercontent.com
    audience: https://github.com/my-organisation
    conditions:
      - jwt.ref == "refs/heads/main"  # restrict to workflows from the main branch
      - jwt.environment == "production"  # restrict to the "production" gihub environment
...
```