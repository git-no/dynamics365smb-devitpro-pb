---
title: "JsonValue.AsChar() Method"
description: "Converts the value in a JsonValue to a Char data type."
ms.author: solsen
ms.date: 08/26/2024
ms.topic: reference
author: SusanneWindfeldPedersen
ms.reviewer: solsen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# JsonValue.AsChar() Method
> **Version**: _Available or changed with runtime version 1.0._

Converts the value in a JsonValue to a Char data type.


## Syntax
```AL
Result :=   JsonValue.AsChar()
```
## Parameters
*JsonValue*  
&emsp;Type: [JsonValue](jsonvalue-data-type.md)  
An instance of the [JsonValue](jsonvalue-data-type.md) data type.  

## Return Value
*Result*  
&emsp;Type: [Char](../char/char-data-type.md)  
If the JsonValue does not contain a number which can be converted without loss of precision to a Char, the operation will fail with a run-time error.


[//]: # (IMPORTANT: END>DO_NOT_EDIT)


## Related information
[JsonValue Data Type](jsonvalue-data-type.md)  
[Get Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)