---
title: Get applyVendorEntries  
description: Gets an apply vendor entry object in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.topic: reference
ms.devlang: al
ms.date: 05/31/2024
ms.author: solsen
ms.reviewer: solsen
---

# Get applyVendorEntries

[!INCLUDE[api_v2_note](../../../includes/api_v2_note.md)]

Retrieves the properties and relationships of an apply vendor entry object for [!INCLUDE[prod_short](../../../includes/prod_short.md)].

## HTTP request

Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../../v2.0/endpoints-apis-for-dynamics.md).

```
GET businesscentralPrefix/companies({id})/vendorPaymentJournals({id})/vendorPayments({id})/applyVendorEntries({id})
```

## Request headers

|Header|Value|
|------|-----|
|Authorization  |Bearer {token}. Required. |

## Request body

Don't supply a request body for this method.

## Response

If successful, this method returns a ```200 OK``` response code and an **applyVendorEntry** object in the response body.

## Example

**Request**

Here's an example of the request.

```json
GET https://{businesscentralPrefix}/api/v2.0/companies({id})/applyVendorEntries({id})
```

**Response**
Here's an example of the response.


```json
{
    "id" : "5d115c9c-44e3-ea11-bb43-000d3a2feca1",
    "applied" : true,
    "appliesToId" : "1e8cb9c0-44e3-ea11-bb43-000d3a2feca1",
    "postingDate" : "2020-10-05",
    "documentType" : "Invoice",
    "documentNumber" : "2001",
    "externalDocumentNumber" : "2001",
    "vendorNumber" : "10000",
    "vendorName" : "First Up Consultants",
    "description" : "",
    "remainingAmount" : 0
}
```

## Remarks

This resource type requires [!INCLUDE[prod_short](../../../includes/prod_short.md)] version 18.0.

## Related information

[Tips for working with the APIs](/dynamics365/business-central/dev-itpro/developer/devenv-connect-apps-tips)  
[applyVendorEntry](../resources/dynamics_applyVendorEntry.md)
