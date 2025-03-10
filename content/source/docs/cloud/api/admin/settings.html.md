---
layout: "cloud"
page_title: "Terraform Enterprise Settings - API Docs - Terraform Cloud"
---

[200]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200
[201]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/201
[202]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/202
[204]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/204
[400]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/400
[401]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401
[403]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/403
[404]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/404
[409]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/409
[412]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/412
[422]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/422
[429]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429
[500]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500
[504]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/504
[JSON API document]: /docs/cloud/api/index.html#json-api-documents
[JSON API error object]: http://jsonapi.org/format/#error-objects

# Terraform Enterprise Settings API

-> **Terraform Enterprise feature:** The admin API is exclusive to Terraform Enterprise, and can only be used by the admins and operators who install and maintain their organization's Terraform Enterprise instance.

-> These API endpoints are available in Terraform Enterprise as of version 201807-1.

## List General Settings
`GET /api/v2/admin/general-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "general-settings"`) | Successfully listed General settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action


### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request GET \
  https://app.terraform.io/api/v2/admin/general-settings
```

### Sample Response

```json
{
  "data": {
    "id": "general",
    "type": "general-settings",
    "attributes": {
      "limit-user-organization-creation": true,
      "support-email-address": "support@hashicorp.com",
      "api-rate-limiting-enabled": true,
      "api-rate-limit": 30
    }
  }
}
```

## Update General Settings
`PATCH /api/v2/admin/general-settings`

Status  | Response                                     | Reason
--------|----------------------------------------------|----------
[200][] | [JSON API document][] (`type: "general-settings"`) | Successfully updated the General settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action
[422][] | [JSON API error object][]                    | Malformed request body (missing attributes, wrong types, etc.)


### Request Body

This PATCH endpoint requires a JSON object with the following properties as a request payload.

Key path                                          | Type     | Default                   | Description
--------------------------------------------------|----------|---------------------------|------------
`data.attributes.limit-user-organization-creation`| bool     | `true`                    | When set to `true`, limits the ability to create organizations to users with the `site-admin` permission only.
`data.attributes.support-email-address`           | string   | `"support@hashicorp.com"` | The support address for outgoing emails.
`data.attributes.api-rate-limiting-enabled`       | bool     | `true`                    | Whether or not rate limiting is enabled for API requests. To learn more about API Rate Limiting, refer to the [rate limiting documentation][]
`data.attributes.api-rate-limit`                  | integer  | 30                        | The number of allowable API requests per second for any client. This value cannot be less than 30. To learn more about API Rate Limiting, refer to the [rate limiting documentation][]

[rate limiting documentation]: ../index.html#rate-limiting

### Sample Payload

```json
{
  "data": {
    "attributes": {
      "limit-user-organization-creation": true,
      "support-email-address": "support@hashicorp.com",
      "api-rate-limiting-enabled": true,
      "api-rate-limit": 50
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request PATCH \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/general-settings
```

### Sample Response

```json
{
  "data": {
    "id":"general",
    "type":"general-settings",
    "attributes": {
      "limit-user-organization-creation": true,
      "support-email-address": "support@hashicorp.com",
      "api-rate-limiting-enabled": true,
      "api-rate-limit": 50
    }
  }
}
```

## List Cost Estimation Settings

`GET /api/v2/admin/cost-estimation-settings`

Status  | Response                                                   | Reason
--------|------------------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "cost-estimation-settings"`) | Successfully listed Cost Estimation settings
[404][] | [JSON API error object][]                                  | User unauthorized to perform action

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request GET \
  https://app.terraform.io/api/v2/admin/cost-estimation-settings
```

### Sample Response

```json
{
  "data": {
    "id": "cost-estimation",
    "type": "cost-estimation-settings",
    "attributes": {
      "enabled": true,
      "aws-access-key-id": "AKIAIOSFODNN7EXAMPLE",
      "aws-secret-key": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
      "gcp-credentials": "{\"private_key\":\"-----BEGIN PRIVATE KEY-----\\n....=\\n-----END PRIVATE KEY-----\",\"private_key_id\":\"some_id\",...}"
    }
  }
}
```

## Update Cost Estimation Settings

`PATCH /api/v2/admin/cost-estimation-settings`

Status  | Response                                                   | Reason
--------|------------------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "cost-estimation-settings"`) | Successfully updated Cost Estimation settings
[404][] | [JSON API error object][]                                  | User unauthorized to perform action
[422][] | [JSON API error object][]                                  | Malformed request body (missing attributes, wrong types, etc.)

### Request Body

This PATCH endpoint requires a JSON object with the following properties as a request payload.

If `data.attributes.enabled` is set to `true`, there must be at least one set of credentials populated with valid values. For example, either both `aws-access-key-id` and `aws-secret-key` must be set, _or_ `gcp-credentials` must be set.

See [SAML Configuration](../../saml/configuration.html) for more details on attribute values.

Key path                            | Type   | Default | Description
------------------------------------|--------|---------|------------
`data.attributes.enabled`           | bool   | `false` | Allows organizations to opt-in to the Cost Estimation feature.
`data.attributes.aws-access-key-id` | string |         | An AWS Access Key ID that the Cost Estimation feature will use to authorize to AWS's Pricing API.
`data.attributes.aws-secret-key`    | string |         | An AWS Secret Key that the Cost Estimation feature will use to authorize to AWS's Pricing API.
`data.attributes.gcp-credentials`   | string |         | A JSON string containing GCP credentials that the Cost Estimation feature will use to authorize to the Google Cloud Platform's Pricing API. This must be the contents of a valid JSON key that is downloaded when [creating a Service Account in GCP](https://cloud.google.com/video-intelligence/docs/common/auth#set_up_a_service_account).

```json
{
  "data": {
    "attributes": {
      "enabled": true,
      "aws-access-key-id": "AKIAIOSFODNN7EXAMPLE",
      "aws-secret-key": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
      "gcp-credentials": "{\"private_key\":\"-----BEGIN PRIVATE KEY-----\\n....=\\n-----END PRIVATE KEY-----\",\"private_key_id\":\"some_id\",...}"
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request PATCH \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/cost-estimation-settings
```

### Sample Response

```json
{
  "data": {
    "id":"cost-estimation",
    "type":"cost-estimation-settings",
    "attributes": {
      "enabled": true,
      "aws-access-key-id": "AKIAIOSFODNN7EXAMPLE",
      "aws-secret-key": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
      "gcp-credentials": "{\"private_key\":\"-----BEGIN PRIVATE KEY-----\\n....=\\n-----END PRIVATE KEY-----\",\"private_key_id\":\"some_id\",...}"
    }
  }
}
```

## List SAML Settings
`GET /api/v2/admin/saml-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "saml-settings"`) | Successfully listed SAML settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action


### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request GET \
  https://app.terraform.io/api/v2/admin/saml-settings
```

### Sample Response

```json
{
  "data": {
    "id": "saml",
    "type": "saml-settings",
    "attributes": {
      "enabled": true,
      "debug": false,
      "old-idp-cert": null,
      "idp-cert": "SAMPLE-CERTIFICATE",
      "slo-endpoint-url": "https://example.com/slo",
      "sso-endpoint-url": "https://example.com/sso",
      "attr-username": "Username",
      "attr-groups": "MemberOf",
      "attr-site-admin": "SiteAdmin",
      "site-admin-role": "site-admins",
      "sso-api-token-session-timeout": 1209600,
      "acs-consumer-url": "https://example.com/users/saml/auth",
      "metadata-url": "https://example.com/users/saml/metadata"
    }
  }
}
```

## Update SAML Settings
`PATCH /api/v2/admin/saml-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "saml-settings"`) | Successfully updated SAML settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action
[422][] | [JSON API error object][]                    | Malformed request body (missing attributes, wrong types, etc.)


### Request Body

This PATCH endpoint requires a JSON object with the following properties as a request payload.

If `data.attributes.enabled` is set to `true`, all remaining attributes must have valid values. You can omit attributes if they have a default value, or if a value was set by a previous update. Omitted attributes keep their previous values.

See [SAML Configuration](/docs/enterprise/saml/configuration.html) for more details on attribute values.

Key path                    | Type   | Default | Description
----------------------------|--------|---------|------------
`data.attributes.enabled`    | bool   | `false` | Allows SAML to be used. If true, all remaining attributes must have valid values.
`data.attributes.debug`    | bool   | `false` | Enables a SAML debug dialog that allows an admin to see the SAMLResponse XML and processed values during login.
`data.attributes.idp-cert`   | string |         | Identity Provider Certificate specifies the PEM encoded X.509 Certificate as provided by the IdP configuration.
`data.attributes.slo-endpoint-url` | string |         | Single Log Out URL specifies the HTTPS endpoint on your IdP for single logout requests. This value is provided by the IdP configuration.
`data.attributes.sso-endpoint-url` | string |         | Single Sign On URL specifies the HTTPS endpoint on your IdP for single sign-on requests. This value is provided by the IdP configuration.
`data.attributes.attr-username`| string | `"Username"` | Username  Attribute Name specifies the name of the SAML attribute that determines the user's username.
`data.attributes.attr-groups`| string | `"MemberOf"` | Team Attribute Name specifies the name of the SAML attribute that determines team membership.
`data.attributes.attr-site-admin`| string |`"SiteAdmin"`| Specifies the role for site admin access. Overrides the "Site Admin Role" method.
`data.attributes.site-admin-role`| string |`"site-admins"`| Specifies the role for site admin access, provided in the list of roles sent in the Team Attribute Name attribute.
`data.attributes.sso-api-token-session-timeout`| integer | 1209600 | Specifies the Single Sign On session timeout in seconds. Defaults to 14 days.

```json
{
  "data": {
    "attributes": {
      "enabled": true,
      "debug": false,
      "idp-cert": "NEW-CERTIFICATE",
      "slo-endpoint-url": "https://example.com/slo",
      "sso-endpoint-url": "https://example.com/sso",
      "attr-username": "Username",
      "attr-groups": "MemberOf",
      "attr-site-admin": "SiteAdmin",
      "site-admin-role": "site-admins",
      "sso-api-token-session-timeout": 1209600
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request PATCH \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/saml-settings
```

### Sample Response

```json
{
  "data": {
    "id":"saml",
    "type":"saml-settings",
    "attributes": {
      "enabled": true,
      "debug": false,
      "old-idp-cert": "SAMPLE-CERTIFICATE",
      "idp-cert": "NEW-CERTIFICATE",
      "slo-endpoint-url": "https://example.com/slo",
      "sso-endpoint-url": "https://example.com/sso",
      "attr-username": "Username",
      "attr-groups": "MemberOf",
      "attr-site-admin": "SiteAdmin",
      "site-admin-role": "site-admins",
      "sso-api-token-session-timeout": 1209600,
      "acs-consumer-url": "https://example.com/users/saml/auth",
      "metadata-url": "https://example.com/users/saml/metadata"
    }
  }
}
```

## Revoke previous SAML IdP Certificate

`POST /api/v2/admin/saml-settings/actions/revoke-old-certificate`

When reconfiguring the IdP certificate, Terraform Enterprise will retain the old IdP certificate to allow for a rotation period. This PUT endpoint will revoke the older IdP certificate when the new IdP certificate is known to be functioning correctly.

See [SAML Configuration](/docs/enterprise/saml/configuration.html) for more details.

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request POST \
  https://app.terraform.io/api/v2/admin/saml-settings/actions/revoke-old-certificate
```

### Sample Response

```json
{
  "data": {
    "id":"saml",
    "type":"saml-settings",
    "attributes": {
      "enabled": true,
      "debug": false,
      "old-idp-cert": null,
      "idp-cert": "NEW-CERTIFICATE",
      "slo-endpoint-url": "https://example.com/slo",
      "sso-endpoint-url": "https://example.com/sso",
      "attr-username": "Username",
      "attr-groups": "MemberOf",
      "attr-site-admin": "SiteAdmin",
      "site-admin-role": "site-admins",
      "sso-api-token-session-timeout": 1209600,
      "acs-consumer-url": "https://example.com/users/saml/auth",
      "metadata-url": "https://example.com/users/saml/metadata"
    }
  }
}
```

## List SMTP Settings
`GET /api/v2/admin/smtp-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "smtp-settings"`) | Successfully listed SMTP settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action


### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request GET \
  https://app.terraform.io/api/v2/admin/smtp-settings
```

### Sample Response

```json
{
  "data": {
    "id": "smtp",
    "type": "smtp-settings",
    "attributes": {
      "enabled": true,
      "host": "example.com",
      "port": 25,
      "sender": "sample_user@example.com",
      "auth": "login",
      "username": "sample_user"
    }
  }
}
```

## Update SMTP Settings
`PATCH /api/v2/admin/smtp-settings`

When a request to this endpoint is submitted, a test message will be sent to the specified `test-email-address`. If the test message delivery fails, the API will return an error code indicating the reason for the failure.

Status  | Response                                     | Reason
--------|----------------------------------------------|----------
[200][] | [JSON API document][] (`type: "smtp-settings"`) | Successfully updated the SMTP settings
[401][] | [JSON API error object][]                    | SMTP user credentials are invalid
[404][] | [JSON API error object][]                    | User unauthorized to perform action
[422][] | [JSON API error object][]                    | Malformed request body (missing attributes, wrong types, etc.)
[500][] | [JSON API error object][]                    | SMTP server returned a server error
[504][] | [JSON API error object][]                    | SMTP server timed out


### Request Body

This PATCH endpoint requires a JSON object with the following properties as a request payload.

If `data.attributes.enabled` is set to `true`, all remaining attributes must have valid values. You can omit attributes if they have a default value, or if a value was set by a previous update. Omitted attributes keep their previous values.

Key path                    | Type   | Default | Description
----------------------------|--------|---------|------------
`data.attributes.enabled`   | bool   | `false` | Allows SMTP to be used. If true, all remaining attributes must have valid values.
`data.attributes.host`      | string |         | The host address of the SMTP server.
`data.attributes.port`      | integer|         | The port of the SMTP server.
`data.attributes.sender`    | string |         | The desired sender address.
`data.attributes.auth`      | string | `"none"`| The authentication type. Valid values are `"none"`, `"plain"`, and `"login"`.
`data.attributes.username`  | string |         | The username used to authenticate to the SMTP server. Only required if `data.attributes.auth` is set to `"login"` or `"plain"`.
`data.attributes.password`  | string |         | The username used to authenticate to the SMTP server. Only required if `data.attributes.auth` is set to `"login"` or `"plain"`.
`data.attributes.test-email-address`| string     |  | The email address to send a test message to. Not persisted and only used during testing.

### Sample Payload

```json
{
  "data": {
    "attributes": {
      "enabled": true,
      "host": "example.com",
      "port": 25,
      "sender": "sample_user@example.com",
      "auth": "login",
      "username": "sample_user",
      "password": "sample_password",
      "test-email-address": "test@example.com"
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request PATCH \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/smtp-settings
```

### Sample Response

```json
{
  "data": {
    "id":"smtp",
    "type":"smtp-settings",
    "attributes": {
      "enabled": true,
      "host": "example.com",
      "port": 25,
      "sender": "sample_user@example.com",
      "auth": "login",
      "username": "sample_user"
    }
  }
}
```

## List Twilio Settings
`GET /api/v2/admin/twilio-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "twilio-settings"`) | Successfully listed Twilio settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action


### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request GET \
  https://app.terraform.io/api/v2/admin/twilio-settings
```

### Sample Response

```json
{
  "data": {
    "id": "twilio",
    "type": "twilio-settings",
    "attributes": {
      "enabled": true,
      "account-sid": "12345abcd",
      "from-number": "555-555-5555"
    }
  }
}
```

## Update Twilio Settings
`PATCH /api/v2/admin/twilio-settings`

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | [JSON API document][] (`type: "twilio-settings"`) | Successfully listed Twilio settings
[404][] | [JSON API error object][]                    | User unauthorized to perform action
[422][] | [JSON API error object][]                    | Malformed request body (missing attributes, wrong types, etc.)


### Request Body

This PATCH endpoint requires a JSON object with the following properties as a request payload.

If `data.attributes.enabled` is set to `true`, all remaining attributes must have valid values. You can omit attributes if they have a default value, or if a value was set by a previous update. Omitted attributes keep their previous values.

Key path                    | Type   | Default | Description
----------------------------|--------|---------|------------
`data.attributes.enabled`    | bool   | `false` | Allows Twilio to be used. If true, all remaining attributes must have valid values.
`data.attributes.account-sid`  | string   |  | The Twilio account id.
`data.attributes.auth-token` | string   |  | The Twilio authentication token.
`data.attributes.from-number`| string     |  | The Twilio registered phone number that will be used to send the message.

```json
{
  "data": {
    "attributes": {
      "enabled": true,
      "account-sid": "12345abcd",
      "auth-token": "sample_token",
      "from-number": "555-555-5555"
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request PATCH \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/twilio-settings
```

### Sample Response

```json
{
  "data": {
    "id":"twilio",
    "type":"twilio-settings",
    "attributes": {
      "enabled": true,
      "account-sid": "12345abcd",
      "from-number": "555-555-5555"
    }
  }
}
```

## Verify Twilio Settings
`POST /api/v2/admin/twilio-settings/verify`

Uses the `test-number` attribute to send a test SMS when Twilio is enabled.

Status  | Response                                         | Reason
--------|--------------------------------------------------|-------
[200][] | none | Twilio test message sent successfully
[400][] | [JSON API error object][]                    | Verification settings invalid (missing test number, Twilio disabled, etc.)
[404][] | [JSON API error object][]                    | User unauthorized to perform action


### Request Body

This POST endpoint requires a JSON object with the following properties as a request payload.

Key path                    | Type   | Default | Description
----------------------------|--------|---------|------------
`data.attributes.test-number`| string     |  | The target phone number for the test SMS. Not persisted and only used during testing.

```json
{
  "data": {
    "attributes": {
      "test-number": "555-555-0000"
    }
  }
}
```

### Sample Request

```shell
curl \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/vnd.api+json" \
  --request POST \
  --data @payload.json \
  https://app.terraform.io/api/v2/admin/twilio-settings/verify
```
