---
title: "System.GetUrl(ClientType [, Text] [, ObjectType] [, Integer] [, Record] [, Boolean] [, Text]) Method"
description: "Generates a URL for the specified client target that is based on the configuration of the server instance."
ms.author: solsen
ms.date: 02/18/2025
ms.topic: reference
author: SusanneWindfeldPedersen
ms.reviewer: solsen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# System.GetUrl(ClientType [, Text] [, ObjectType] [, Integer] [, Record] [, Boolean] [, Text]) Method
> **Version**: _Available or changed with runtime version 15.0._

Generates a URL for the specified client target that is based on the configuration of the server instance. If the code runs in a multitenant deployment architecture, the generated URL will automatically apply to the tenant ID of the current user.


## Syntax
```AL
String :=   System.GetUrl(ClientType: ClientType [, Company: Text] [, ObjectType: ObjectType] [, ObjectId: Integer] [, Record: Record] [, UseFilters: Boolean] [, Layout: Text])
```
> [!NOTE]
> This method can be invoked without specifying the data type name.
## Parameters
*ClientType*  
&emsp;Type: [ClientType](../clienttype/clienttype-option.md)  
Specifies the client that you want to generate the URL for. If you want to generate a URL that depends on the client that the user is accessing the URL from, choose Current. A runtime error occurs if the ClientType is set to SOAP or OData but the specified object type and ID has not been published as a web service.  

*[Optional] Company*  
&emsp;Type: [Text](../text/text-data-type.md)  
Specifies the company that the URL must contain. If you do not specify a company, the URL will run in the user’s current company.  

*[Optional] ObjectType*  
&emsp;Type: [ObjectType](../objecttype/objecttype-option.md)  
Specifies the object type that the URL must open. Valid values are: Table, Page, Report, Codeunit, Query, or XmlPort. If you specify an object type, you must also specify an object ID in the ObjectId parameter. Otherwise, the user will see a runtime error. If you set the ObjectType parameter to Page, you can also specify a record variable in the Record parameter.  

*[Optional] ObjectId*  
&emsp;Type: [Integer](../integer/integer-data-type.md)  
Specifies the ID of the specified object type that the URL must open.  

*[Optional] Record*  
&emsp;Type: [Record](../record/record-data-type.md)  
Specifies the Record variable that specifies which record to open.  

*[Optional] UseFilters*  
&emsp;Type: [Boolean](../boolean/boolean-data-type.md)  
Specifies whether to include filters that are defined on the object as a text string in the URL. Note, UseFilters is supported only for ClientType: Desktop, OData, Phone, Tablet, Web, Windows, Default, ODataV4, Teams and Current, when CurrenClientType is one of the ClientType mentioned. An empty string is returned otherwise.  

*[Optional] Layout*  
&emsp;Type: [Text](../text/text-data-type.md)  
Specifies the page layout to open the page in (Supported List, TallTiles, Tiles, Analysis).  


## Return Value
*String*  
&emsp;Type: [Text](../text/text-data-type.md)  



[//]: # (IMPORTANT: END>DO_NOT_EDIT)
## Related information
[System data type](system-data-type.md)  
[Getting started with AL](../../devenv-get-started.md)  
[Developing extensions](../../devenv-dev-overview.md)