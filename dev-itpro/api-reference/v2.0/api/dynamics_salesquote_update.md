---
title: Update salesQuotes  
description: Updates a sales quote object in Dynamics 365 Business Central.
 
author: SusanneWindfeldPedersen

ms.topic: reference
ms.devlang: al
ms.date: 05/31/2024
ms.author: solsen
ms.reviewer: solsen
---

# Update salesQuotes

[!INCLUDE[api_v2_note](../../../includes/api_v2_note.md)]

Update the properties of a sales quotes object for [!INCLUDE[prod_short](../../../includes/prod_short.md)].

## HTTP request
Replace the URL prefix for [!INCLUDE[prod_short](../../../includes/prod_short.md)] depending on environment following the [guideline](../../v2.0/endpoints-apis-for-dynamics.md).

```
PATCH businesscentralPrefix/companies({id})/salesQuotes({id})
```

## Request headers

|Header|Value|
|------|-----|
|Authorization |Bearer {token}. Required.|
|Content-Type  |application/json|
|If-Match      |Required. When this request header is included and the eTag provided doesn't match the current tag on the salesQuote, the salesQuote won't be updated. |

## Request body
In the request body, supply the values for relevant fields that should be updated. Existing properties that aren't included in the request body will maintain their previous values or be recalculated based on changes to other property values. For best performance you shouldn't include existing values that haven't changed.

## Response
If successful, this method returns a ```200 OK``` response code and an updated **salesQuotes** object in the response body.

## Example

**Request**

Here's an example of the request.
```json
PATCH https://{businesscentralPrefix}/api/v2.0/companies({id})/salesQuotes({id})
Content-type: application/json

{
  "paymentTermsId": "3bb5b4b6-ea4c-43ca-ba1c-3b69e29a6668"
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
  "id": "id-value",
  "number": "1006",
  "externalDocumentNumber": "",
  "documentDate": "2019-01-24",
  "dueDate": "2019-01-24",
  "customerId": "customerId-value",
  "contactId": "",
  "customerNumber": "10000",
  "customerName": "Coho Winery",
  "billingPostalAddress": {
    "street": "",
    "city": "",
    "state": "",
    "countryLetterCode": "",
    "postalCode": ""
  },
  "currencyId": "currencyId-value",
  "currencyCode": "GBP",
  "paymentTermsId": "3bb5b4b6-ea4c-43ca-ba1c-3b69e29a6668",
  "shipmentMethodId": "shipmentMethodId-value",
  "shipmentMethod": "EXW",
  "salesperson": "",
  "discountAmount": 0,
  "totalAmountExcludingTax": 6825.6,
  "totalTaxAmount": 682.56,
  "totalAmountIncludingTax": 7508.16,
  "status": "Open",
  "sentDate": "0001-01-01T00:00:00Z",
  "validUntilDate": "0001-01-01",
  "acceptedDate": "0001-01-01",  
  "lastModifiedDateTime": "2017-03-17T19:02:22.043Z"
}
```

## Related information
[Tips for working with the APIs](../../../developer/devenv-connect-apps-tips.md)    
[salesquote](../resources/dynamics_salesquote.md)    
[Get salesquote](dynamics_salesquote_Get.md)    
[Delete salesquote](dynamics_salesquote_Delete.md)    
[Create salesquote](dynamics_salesquote_Create.md)    
