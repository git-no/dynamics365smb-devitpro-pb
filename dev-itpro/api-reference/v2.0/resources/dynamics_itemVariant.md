---
title: itemVariant resource type  
description: An item variant object in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.topic: reference
ms.devlang: al
ms.date: 04/28/2025
ms.author: solsen
ms.reviewer: solsen
---

# itemVariant resource type

[!INCLUDE[api_v2_note](../../../includes/api_v2_note.md)]

Represents an item variant in [!INCLUDE[prod_short](../../../includes/prod_short.md)].

> [!NOTE]
> For information about enabling APIs for [!INCLUDE[prod_short](../../../includes/prod_short.md)] see [Enabling the APIs for Dynamics 365 Business Central](../enabling-apis-for-dynamics-nav.md).

## Methods

| Method | Return Type|Description |
|:--------------------|:-----------|:-------------------------|
|[GET itemVariant](../api/dynamics_itemvariant_get.md)|itemVariant|Gets a item variant object.|
|[DELETE itemVariant](../api/dynamics_itemvariant_delete.md)|none|Deletes a item variant object.|
|[POST itemVariant](../api/dynamics_itemvariant_create.md)|itemVariant|Creates a item variant object.|
|[PATCH itemVariant](../api/dynamics_itemvariant_update.md)|itemVariant|Updates a item variant object.|

## Navigation

| Navigation |Return Type| Description |
|:----------|:----------|:-----------------|
|[item](dynamics_item.md)|item |Gets the item of the itemVariant.|

## Properties

| Property           | Type   |Description     |
|:-------------------|:-------|:---------------|
|id|GUID|The unique ID of the item variant. Non-editable.|
|itemId|GUID|The ID of the item in the item variant.|
|itemNumber|string|The name of the item in the item variant.|
|code|string|The code of the item variant.|
|description|string|Specifies the description of the item variant.|
|lastModifiedDateTime|datetime|The last datetime the item variant was modified. Read-Only.|

## JSON representation

Here's a JSON representation of the itemVariant resource.


```json
{
    "id": "GUID",
    "itemId": "GUID",
    "itemNumber": "string",
    "code": "string",
    "description": "string",
    "lastModifiedDateTime": "datetime"
}
```

## Related information

[GET itemVariant](../api/dynamics_itemVariant_Get.md)  
[DELETE itemVariant](../api/dynamics_itemVariant_Delete.md)  
[POST itemVariant](../api/dynamics_itemVariant_Create.md)  
[PATCH itemVariant](../api/dynamics_itemVariant_Update.md)
