---
title: fixedAssetLocation resource type
description: A fixed asset location object in Dynamics 365 Business Central.
author: SusanneWindfeldPedersen
ms.topic: reference
ms.devlang: al
ms.date: 04/28/2025
ms.author: solsen
ms.reviewer: solsen
---

# fixedAssetLocation resource type

Represents a fixed asset location in [!INCLUDE[prod_short](../../../includes/prod_short.md)].

> [!NOTE]
> For information about enabling APIs for [!INCLUDE[prod_short](../../../includes/prod_short.md)] see [Enabling the APIs for Dynamics 365 Business Central](../enabling-apis-for-dynamics-nav.md).

## Methods

| Method | Return Type|Description |
|:--------------------|:-----------|:-------------------------|
|[GET fixedAssetLocation](../api/dynamics_fixedassetlocation_get.md)|fixedAssetLocation|Gets a fixed asset location object.|
|[DELETE fixedAssetLocation](../api/dynamics_fixedassetlocation_delete.md)|none|Deletes a fixed asset location object.|
|[POST fixedAssetLocation](../api/dynamics_fixedassetlocation_create.md)|fixedAssetLocation|Creates a fixed asset location object.|
|[PATCH fixedAssetLocation](../api/dynamics_fixedassetlocation_update.md)|fixedAssetLocation|Updates a fixed asset location object.|

## Properties

| Property           | Type   |Description     |
|:-------------------|:-------|:---------------|
|id|GUID|The unique ID of the fixed asset location. Non-editable.|
|code|string|The code of the fixed asset location.|
|displayName|string|Specifies the fixed asset location's name. This name will appear on all sales documents for the fixed asset location.|
|lastModifiedDateTime|datetime|The last datetime the fixed asset location was modified. Read-Only.|

## JSON representation

Here's a JSON representation of the fixedAssetLocation resource.


```json
{
    "id": "GUID",
    "code": "string",
    "displayName": "string",
    "lastModifiedDateTime": "datetime"
}
```

## Related information

[GET fixedAssetLocation](../api/dynamics_fixedassetlocation_get.md)  
[DELETE fixedAssetLocation](../api/dynamics_fixedassetlocation_delete.md)  
[POST fixedAssetLocation](../api/dynamics_fixedassetlocation_create.md)  
[PATCH fixedAssetLocation](../api/dynamics_fixedassetlocation_update.md)
