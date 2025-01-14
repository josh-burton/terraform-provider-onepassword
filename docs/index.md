---
layout: ""
page_title: "Provider: 1Password"
description: |-
  Use the 1Password Terraform provider to reference, create, or update logins, passwords, and database items in your 1Password vaults.
---

# 1Password Terraform provider

Use the 1Password Terraform provider to reference, create, or update items in your existing vaults using [1Password Secrets Automation](https://1password.com/secrets).

The 1Password Terraform provider supports using both [1Password Connect Server](https://developer.1password.com/docs/secrets-automation/#1password-connect-server)
and [1Password CLI](https://developer.1password.com/docs/cli).

You must install [1Password CLI](https://developer.1password.com/docs/cli) on the machine running Terraform to use it. Refer to the
[Terraform documentation](https://developer.hashicorp.com/terraform/cloud-docs/run/install-software#only-install-standalone-binaries) to learn how to install 1Password CLI on Terraform Cloud.

## Use with 1Password CLI

Retry mechanism is implemented when using the provider with 1Password CLI. The reason for having a retry mechanism is that 1Password doesn't allow parallel modification on the items located in the same vault.
Note that each retry fast forwards to the [service account rate limit](https://developer.1password.com/docs/service-accounts/rate-limits/) if use with service account.

It's recommended to limit the number of parallel resource operations. It can be done by using `-parallelism=n` flag when running `terraform apply`, where `n` is the number of parallel resource operations (the default is `10`).
```
terraform apply `-parallelism=n`
```

### Authenticate CLI with service account

To authenticate CLI with service account, set `service_account_token` in the provider configuration.

### Authenticate the CLI with user account using biometric unlock

To authenticate the CLI with user account using biometric unlock:
1. [Turn on the app integration](https://developer.1password.com/docs/cli/app-integration/#step-1-turn-on-the-app-integration)
2. In the terminal run `op account ls` to find sign-in address or account ID. It will print similar output in the console:
```
URL                        EMAIL                                         USER ID
acme.dev.com               test.user@acme.com                            HERE_WILL_BE_REAL_USER_ID
acme.prod.com              prod.user@acme.com                            HERE_WILL_BE_REAL_USER_ID
```
3. Set `account` in the provider configuration with the `URL` or `USER ID` value from the previous step.
4. When the biometric unlock popup appears while running terraform command, [authenticate it using fingerprint or password](https://developer.1password.com/docs/cli/app-integration/#step-2-enter-any-command-to-sign-in).

## Use with 1Password Connect

To use the provider with 1Password Connect you need to
1. [Deploy your Connect server](https://developer.1password.com/docs/connect/get-started#deployment)
2. Set `url` and `token` in the provider configuration.

## Example Usage

```terraform
provider "onepassword" {
  url                   = "http://localhost:8080"
  token                 = "CONNECT_TOKEN"
  service_account_token = "SERVICE_ACCOUNT_TOKEN"
  account               = "ACCOUNT_ID_OR_SIGN_IN_ADDRESS"
  op_cli_path           = "OP_CLI_PATH"
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Optional

- `account` (String) A valid account's sign-in address or ID to use biometrics unlock. Can also be sourced from OP_ACCOUNT. Must be set to use with biometric unlock.
- `op_cli_path` (String) The path to the 1Password CLI binary. Can also be sourced from OP_CLI_PATH. Defaults to `op`.
- `service_account_token` (String) A valid token for your 1Password Service Account. Can also be sourced from OP_SERVICE_ACCOUNT_TOKEN. Must be set to use with 1Password service account.
- `token` (String) A valid token for your 1Password Connect API. Can also be sourced from OP_CONNECT_TOKEN. Must be set to use with 1Password Connect server.
- `url` (String) The HTTP(S) URL where your 1Password Connect API can be found. Must be provided through the OP_CONNECT_HOST environment variable if this attribute is not set. Must be set to use with 1Password Connect server.
