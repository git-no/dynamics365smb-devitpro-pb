---
title: Create subscriptions  
description: Creates a subscriptions object in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.topic: reference
ms.devlang: al
ms.date: 11/20/2024
ms.author: solsen
ms.reviewer: solsen
---

# Create subscription

[!INCLUDE[api_v2_note](../../../includes/api_v2_note.md)]

Creates a webhook subscription for [!INCLUDE[prod_short](../../../includes/prod_short.md)]. Will be created only if handshake is successful.

## Custom APIs

If you're subscribing to a custom API page, both the URL you send the subscription HTTP request to and the resource path you wish to subscribe to must include the `<APIPublisher>`, `<APIGroup>`, and `<APIVersion>` elements equivalent to: `api/<APIPublisher>/<APIGroup>/<APIVersion>/subscriptions`. For example, if your API publisher is `pub`, your API group is `grp`, and the version is 1.0, part of the URL will contain these elements `api/pub/grp/v1.0/subscriptions`.

## HTTP request

Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../endpoints-apis-for-dynamics.md).

```
POST businesscentralPrefix/subscriptions
```
## Request headers

|Header|Value|
|------|-----|
|Authorization  |Bearer {token}. Required. |
|Content-Type  |application/json|

## Request body
In the request body, supply a JSON representation of subscription object.

## Response
If successful, this method returns a `201 Created` response code and a **subscription** object in the response body.

## Example

**Request**

Here's an example of the request.
```json
POST https://{businesscentralPrefix}/api/v1.0/subscriptions
Content-type: application/json

{
  "subscriptionId": "c670ea73cacb459bb51dc1740da2f1db",
  "notificationUrl": "https://contoso.com/myCallbacks",
  "resource": "/api/v1.0/companies(f64eba74-dacd-4854-a584-1834f68cfc3a)/customers",
  "userId": "00000000-0000-0000-0000-000000000001",
  "lastModifiedDateTime": "2018-10-12T12:32:35Z",
  "clientState": "optionalvalueof2048",
  "expirationDateTime": "2021-10-15T12:32:35Z",
  "systemCreatedAt": "2017-01-23T00:24:31.766Z",
  "systemCreatedBy": "f2a5738a-44e3-ea11-bb43-000d3a2feca1",
  "systemModifiedAt": "2020-08-21T00:24:31.777Z",
  "systemModifiedBy": "f2a5738a-44e3-ea11-bb43-000d3a2feca1"
}

```

**Response**

Here's an example of the response. 

> [!NOTE]  
> The response object shown here may be truncated for brevity. All of the properties will be returned from an actual call.

```json
{
  "subscriptionId": "c670ea73cacb459bb51dc1740da2f1db",
  "notificationUrl": "https://contoso.com/myCallbacks",
  "resource": "/api/v1.0/companies(f64eba74-dacd-4854-a584-1834f68cfc3a)/customers",
  "userId": "00000000-0000-0000-0000-000000000001",
  "lastModifiedDateTime": "2018-10-12T12:32:35Z",
  "clientState": "optionalvalueof2048",
  "expirationDateTime": "2021-10-15T12:32:35Z",
  "systemCreatedAt": "2017-01-23T00:24:31.766Z",
  "systemCreatedBy": "f2a5738a-44e3-ea11-bb43-000d3a2feca1",
  "systemModifiedAt": "2020-08-21T00:24:31.777Z",
  "systemModifiedBy": "f2a5738a-44e3-ea11-bb43-000d3a2feca1"
}
```

> [!IMPORTANT]  
> Handshake is mandatory when [creating a subscription](dynamics_subscriptions_create.md) and [renewing a subscription](dynamics_subscriptions_update.md). See [Working with Webhooks](../dynamics-subscriptions.md).  


## Related information

[Tips for working with the APIs](../../../developer/devenv-connect-apps-tips.md)  
[Subscriptions](../resources/dynamics_subscriptions.md)  
[Get subscriptions](dynamics_subscriptions_Get.md)  
[Delete subscriptions](dynamics_subscriptions_Delete.md)  
[Update subscriptions](dynamics_subscriptions_Update.md)  
