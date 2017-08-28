---
title: Basic Connection - Reference Documentation
layout: apps
---

Connection is a link between Integromat and 3rd party service/app.

Use this account when authenticating via an API key, username/password,
or when your service does not support OAuth 1/2.

# Index

- [Making requests](#making-requests)
  - [`url`](#url)
  - [`method`](#method)
  - [`headers`](#headers)
  - [`qs`](#qs)
  - [`body`](#body)
  - [`type`](#request-type)
  - [`temp`](#request-temp)
  - [`condition`](#condition)
- [Handling responses](#handling-responses)
  - [`type`](#response-type)
  - [`valid`](#valid)
  - [`error`](#error)
    - [`message`](#error-message)
    - [`type`](#error-type)
    - [`\<status-code>`](#error-status-code)
  - [`temp`](#response-temp)
  - [`data`](#data)
  - [`metadata`](#metadata)
- [IML variables](#iml-variables)
- [Error handling](#error-handling)

# Specification

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
You can make multiple requests by placing above objects in an array.

# Making requests

In order to make a request you have to specify at least a `url` and
`oauth` directives. All other directives are not required.

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
| [**response**](#handling-responses)   | Response Specification                                                       | Collection of directives controlling processing of the response.                 |

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

# Handling responses

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
| [**metadata**](#metadata)    | Metadata Specification                                           | Updates this connection's metadata                                                 |
| [**uid**](#uid)              | [IML String](other/types.md#iml-string)                          | An expression that parses user's id from response body.                         |

## Detailed response directive description

### `type` {#response-type}

{% include_relative directives/response.type.md %}

### `valid`

{% include_relative directives/valid.md %}

### `error`

{% include_relative directives/error.md %}

### `temp` {#response-temp}

{% include_relative directives/response.temp.md %}

### `data` {#data}

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


### `metadata` {#metadata}

The `metadata` directive allows you to save user's name or username (or
any other text field) so that multiple connections of the same type
could be easily recognized. A common practice is to save either username
or email or full name to metadata.

The metadata object has 2 properties: `value` and `type`. `value` is
used to store the value and `type` is used to specify what the value is.
Currently there are only 2 types: `email` and `text`.

**Example**:
{% raw %}
```json
{
    "response": {
        "metadata": {
            "value": "{{body.data.username}}",
            "type": "text"
        }
    }
}
```
{% endraw %}

### `uid` {#uid}

This directive allows you to save the user's remote service Id. This is
required when using Shared Webhooks.

**Example**:
{% raw %}
```json
{
    "response": {
        "uid": "{{body.data.id}}"
    }
}
```
{% endraw %}

# IML variables

{% include_relative sections/iml-variables.md module="connection" type="basic" %}

# Error handling

{% include_relative sections/error-handling.md %}