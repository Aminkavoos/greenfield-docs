---
title: List Objects By Bucket
order: 7
---
# ListObjectsByBucket

## RESTful API Description

This API is used to query a bucket's all objects metadata info. And it supports both `virutal-hosted-style` and `path-style` requests.

## HTTP Request Format

| Description                | Definition                                |
| -------------------------- | ----------------------------------------- |
| Host(virutal-hosted-style) | BucketName.gnfd-testnet-sp-*.bnbchain.org |
| Path(virutal-hosted-style) | /                                         |
| Method                     | GET                                       |

You should set `BucketName` in url host to list objects of the bucket.

## HTTP Request Header

| ParameterName                                                      | Type   | Required | Description                                  |
| ------------------------------------------------------------------ | ------ | -------- | -------------------------------------------- |
| [Authorization](./referenece/gnfd_headers.md#authorization-header) | string | yes      | The authorization string of the HTTP request |

## HTTP Request Parameter

### Path Parameter

The request does not have a path parameter.

### Query Parameter

The request does not have a query parameter.

### Request Body

The request does not have a request body.

## Request Syntax

```HTTP
GET / HTTP/1.1
Host: BucketName.gnfd-testnet-sp-*.bnbchain.org
Authorization: Authorization
```

## HTTP Response Header

The response returns the following HTTP headers.

| ParameterName | Type   | Description                 |
| ------------- | ------ | --------------------------- |
| Content-Type  | string | value is `application/json` |

## HTTP Response Parameter

### Response Body

If the request is successful, the service sends back an HTTP 200 response.

If you failed to send request to get approval, you will get error response body in [XML](./common/error.md#sp-error-response-parameter).

## Response Syntax

```HTTP
HTTP/1.1 200

JSON Body
```

## Examples

The examples given all use virutal-hosted-style.

### Example 1: Query a bucket's objects

```HTTP
GET / HTTP/1.1
Host: myBucket.gnfd-testnet-sp-*.bnbchain.org
Date: Fri, 31 March 2023 17:32:00 GMT
Authorization: authorization string
```

### Sample Response: Query a bucket's objects

```HTTP
HTTP/1.1 200 OK
Date: Fri, 31 March 2023 17:32:10 GMT
Content-Length: 11434

[11434 bytes of object data]
```
