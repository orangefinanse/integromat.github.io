---
title: OAuth 2 Connection - Reference Documentation
layout: apps
---

Connection is a link between Integromat and 3rd party service/app.
OAuth2 connection handles the token exchange automatically.

Before you start configuring you OAuth2 connection, you need to create
an app on a 3rd-party service. When creating an app, use
`https://www.integromat.com/oauth/cb/app` as a callback URL.


# Index

- [Specification](#specification)
- [OAuth 2 authentication process](#oauth-2-authentication-process)
- [Request options](#request-options)
  - [`url`](#url)
  - [`method`](#method)
  - [`headers`](#headers)
  - [`qs`](#qs)
  - [`body`](#body)
  - [`type`](#request-type)
  - [`temp`](#request-temp)
  - [`condition`](#condition)
  - [`oauth`](#oauth)
- [Response options](#response-options)
  - [`type`](#response-type)
  - [`valid`](#valid)
  - [`error`](#error)
    - [`message`](#error-message)
    - [`type`](#error-type)
    - [`\<status-code>`](#error-status-code)
  - [`temp`](#response-temp)
  - [`data`](#data)
- [IML variables](#iml-variables)
- [Error handling](#error-handling)

# Specification

Specifies token exchange and connection validation process. This
specification does **not** inherit from Base.

{% raw %}
```
{
  "preauthorize": Request Specification or Array of Request Specification,
  "authorize": Request Specification,
  "token": Request Specification,
  "info": Request Specification,
  "refresh": Request Specification,
  "invalidate": Request Specification
}
```

Request Specification:
```
{
    "url": String,
    "method": Enum[GET, POST, PUT, DELETE, OPTIONS],
    "qs": Flat Object,
    "headers": Flat Object,
    "body": Object,
    "type": Enum[json, urlencoded, multipart/form-data, binary, text/string/raw]
    "ca": String
    "condition": String|Boolean,
    "response": {
        "type": Enum[json, urlencoded, xml, text/string/raw/binary, automatic]
        "type": {
            "*": Enum[json, urlencoded, xml, text/string/raw/binary, automatic],
            "Number[-Number]": Enum[json, urlencoded, xml, text/string/raw/binary, automatic]
        },
        "temp": Object,
        "data": Object,
        "uid": String (available only in info section),
        "metadata": String (available only in info section)
        "valid": String|Boolean,
        "error": String,
        "error": {
            "message": String,
            "type": Enum[RuntimeError, DataError, RateLimitError, OutOfSpaceError, ConnectionError, InvalidConfigurationError, InvalidAccessTokenError, IncompleteDataError, DuplicateDataError]
            "Number": {
                "message": String,
                "type": Enum[RuntimeError, DataError, RateLimitError, OutOfSpaceError, ConnectionError, InvalidConfigurationError, InvalidAccessTokenError, IncompleteDataError, DuplicateDataError]
            }
        }
    }
}
```
{% endraw %}

# OAuth 2 authentication process

OAuth 2 authentication process consist of multiple steps. You are able
to select the steps you need and ignore the steps that you don't - just
fill in the needed sections and delete unneeded.

| Key              | Type                                      | Description                                                                                                                                                                                |
| ---              | ---                                       | ---                                                                                                                                                                                        |
| **preauthorize** | [Request Specification](#request-options) | Describes a request that should be executed prior to `authorize` directive.                                                                                                                |
| **authorize**    | [Request Specification](#request-options) | Describes authorization process.                                                                                                                                                           |
| **token**        | [Request Specification](#request-options) | Describes a request that exchanges credentials for tokens.                                                                                                                                 |
| **info**         | [Request Specification](#request-options) | Describes a request that validates a connection. The most common way to validate the connection is to call an API's method to get user's information. Most of the APIs have such a method. |
| **refresh**      | [Request Specification](#request-options) | Describes a request that refreshes an access token.                                                                                                                                        |
| **invalidate**   | [Request Specification](#request-options) | Describes a request that invalidates acquired access token.                                                                                                                                |

Each section is responsible for executing its part in the OAuth 2 flow.

In short, you can describe the initial OAuth 2 flow as follows:
```
preauthorize => authorize => token => info
```
with `preauthorize` and `info` sections being optional, and `refresh`
and `invalidate` not being a part of initial OAuth 2 flow.


# Request Options

In order to make a request, you have to specify at least a `url`. All
other directives are not required.

All Available request-related directives are shown in the table below:

| Key                                   | Type                                                                         | Description                                                                      |
| :------------------------------------ | :-----------------------------------------------------------------           | :------------------------------------------------------------------------------- |
| [**url**](#url)                       | [IML String](other/types.md#iml-string)                                      | Specifies the URL that should be called.                                         |
| [**method**](#method)                 | [IML String](other/types.md#iml-string)                                      | Specifies the HTTP method, that should be used when issuing a request.           |
| [**headers**](#headers)               | [IML Flat Object](other/types.md#iml-flat-object)                            | A single level (flat) collection, that specifies request headers.                |
| [**qs**](#qs)                         | [IML Flat Object](other/types.md#iml-flat-object)                            | A single level (flat) collection that specifies request query string parameters. |
| **ca**                                | [IML String](other/types.md#iml-string)                                      | Custom Certificate Authority                                                     |
| [**body**](#body)                     | Any [IML Type](other/types.md#iml-types)                                     | Specifies a request body.                                                        |
| [**type**](#request-type)             | [String](other/types.md#string)                                              | Specifies how data are serialized into body.                                     |
| [**temp**](#request-temp)             | [IML Object](other/types.md#iml-object)                                      | Creates/updates the `temp` variable                                              |
| [**condition**](#condition)           | [IML String](other/types.md#iml-string) or [Boolean](other/types.md#boolean) | Determines if to execute current request or never.                               |
| [**response**](#response-options)   | Response Specification                                                       | Collection of directives controlling processing of the response.                 |

## Multiple Requests

{% include_relative sections/multiple_requests.md %}

## Detailed request directive description

### `url`

{% include_relative directives/url.md module="connection" %}

### `method`

{% include_relative directives/method.md %}

### `headers`

{% include_relative directives/headers.md %}

### `qs`

{% include_relative directives/qs.md %}

### `body`

{% include_relative directives/body.md %}

### `type` {#request-type}

{% include_relative directives/request.type.md %}

### `temp` {#request-temp}

{% include_relative directives/request.temp.md %}

### `condition`

{% include_relative directives/condition.md %}

# Response options

Connections don't have any output. They are used to authenticate the
user in a remote service and to store data about this authentication,
like an API key, or access key or anything else.

Below is the collection of directives controlling processing of the
response. All of them must be placed inside the `response` collection.

| Key                          | Type                                                             | Description                                                                     |
| :--------------------------- | :--------------------------------------------------------------- | :------------------------------------------------------------------------------ |
| [**type**](#response-type)   | [String](other/types.md#string) or Type Specification            | Specifies how data are parsed from body.                                        |
| [**valid**](#valid)          | [IML String](other/types.md#iml-string)                          | An expression that parses whether the response is valid or not.                 |
| [**error**](#error)          | [IML String](other/types.md#iml-string) or Error Specification   | Specifies how the error is shown to the user, if it would occur.                |
| [**temp**](#response-temp)   | [IML Object](other/types.md#iml-object)                          | Creates/updates variable `temp` which you can access in subsequent requests.    |
| [**data**](#data)            | [IML Object](other/types.md#iml-object)                          | Updates this connection's data                                                     |

## Detailed response directive description

### `type` {#response-type}

{% include_relative directives/response.type.md %}

### `valid`

{% include_relative directives/valid.md %}

### `error`

{% include_relative directives/error.md %}

### `temp` {#response-temp}

{% include_relative directives/response.temp.md %}

### `data`

The `data` directive saves data to the connection so that it can be later
accessed from a module through the `connection` variable. It functions
similarly to the `temp` directive, except that `data` is persisted to
the connection.

**Example**:
{% raw %}
```json
{
    "response": {
        "data": {
            "accessToken": "{{body.token}}"
        }
    }
}
```
This `accessToken` can be later accessed in any module that uses this
connection like so:
```json
{
    "url": "http://example.com",
    "qs": {
        "token": "{{connection.accessToken}}"
    }
}
```
{% endraw %}

# IML variables

{% include_relative sections/iml-variables.md module="connection" type="oauth2" %}

# Error handling

{% include_relative sections/error-handling.md %}