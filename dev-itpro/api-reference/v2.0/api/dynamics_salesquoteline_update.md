---
title: Update salesQuoteLines  
description: Updates a sales quote line in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.topic: reference
ms.devlang: al
ms.date: 05/31/2024
ms.author: solsen
ms.reviewer: solsen
---

# Update salesQuoteLines

[!INCLUDE[api_v2_note](../../../includes/api_v2_note.md)]

Update the properties of a sales quote line object for [!INCLUDE[prod_short](../../../includes/prod_short.md)].

## HTTP request
Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../../v2.0/endpoints-apis-for-dynamics.md).

```
PATCH businesscentralPrefix/companies({id})/salesQuotes({id})/salesQuoteLines({salesQuoteLineId})
PATCH businesscentralPrefix/companies({id})/salesQuoteLines({salesQuoteLineId})
```

## Request headers

|Header|Value|
|------|-----|
|Authorization |Bearer {token}. Required.|
|Content-Type  |application/json|
|If-Match      |Required. When this request header is included and the eTag provided doesn't match the current tag on the **salesQuoteLines**, the **salesQuoteLines** won't be updated. |

## Request body
In the request body, supply the values for relevant fields that should be updated. Existing properties that aren't included in the request body will maintain their previous values or be recalculated based on changes to other property values. For best performance you shouldn't include existing values that haven't changed.

## Response
If successful, this method returns a ```200 OK``` response code and an updated **salesQuoteLines** object in the response body.

## Example

**Request**

Here's an example of the request.
```json
PATCH https://{businesscentralPrefix}/api/v2.0/companies({id})/salesQuotes({id})/salesQuoteLines({salesQuoteLineId})
Content-type: application/json

{
  "lineType": "Item"
}
```

**Response**

Here's an example of the response. 

> [!NOTE]  
>   The response object shown here may be truncated for brevity. All of the properties will be returned from an actual call.

```json
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "1c8cb9c0-44e3-ea11-bb43-000d3a2feca1",
    "documentId": "920f5c9c-44e3-ea11-bb43-000d3a2feca1",
    "sequence": 10000,
    "itemId": "04a6738a-44e3-ea11-bb43-000d3a2feca1",
    "accountId": "00000000-0000-0000-0000-000000000000",
    "lineType": "Item",
    "lineObjectNumber": "1936-S",
    "description": "BERLIN Guest Chair, yellow",
    "unitOfMeasureId": "5ca6738a-44e3-ea11-bb43-000d3a2feca1",
    "unitOfMeasureCode": "PCS",
    "unitPrice": 192.8,
    "quantity": 10,
    "discountAmount": 0,
    "discountPercent": 0,
    "discountAppliedBeforeTax": false,
    "amountExcludingTax": 1928,
    "taxCode": "FURNITURE",
    "taxPercent": 5,
    "totalTaxAmount": 96.4,
    "amountIncludingTax": 2024.4,
    "netAmount": 1928,
    "netTaxAmount": 96.4,
    "netAmountIncludingTax": 2024.4,
    "itemVariantId": "00000000-0000-0000-0000-000000000000"
}
```

## Related information
[Tips for working with the APIs](../../../developer/devenv-connect-apps-tips.md)    
[salesquoteline](../resources/dynamics_salesquoteline.md)    
[Get salesquoteline](dynamics_salesquoteline_Get.md)    
[Delete salesquoteline](dynamics_salesquoteline_Delete.md)    
[Create salesquoteline](dynamics_salesquoteline_Create.md)    
