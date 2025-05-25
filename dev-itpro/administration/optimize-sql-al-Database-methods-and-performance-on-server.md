---
title: AL database methods and performance on SQL Server
description: Read about the relationship between basic database methods in AL and SQL statements in Business Central.  
author: jswymer
ms.topic: article
ms.author: jswymer
ms.date: 04/01/2025
ms.reviewer: jswymer
---

# AL database methods and performance on SQL Server

This article describes the relationship between basic database methods in AL and SQL statements.  
  
## Get, Find, FindSet, and Next  

The AL language offers several methods to retrieve record data. In [!INCLUDE[prod_long](../developer/includes/prod_long.md)], records are retrieved using multiple active result sets (MARS). Generally, retrieving records with MARS is faster than with server-side cursors. Additionally, each function is optimized for a specific purpose. To achieve optimal performance, you must use the method that is best suited for a given purpose.  
  
- **Record.Get** is optimized for getting a single record based on primary key values.  
  
- **Record.Find** is optimized for getting a single record based on the primary keys in the record and any filter or range that is set.  
  
- **Record.Find('-')** and **Record.Find('+')** are optimized for reading primarily from a single table when the application might not read all records. Find('-') is implemented by issuing a self-tuning TOP X call, where X can change over time, based on statistics of the number of rows read.  
  
     The following are examples of scenarios in which you should use the Find('-') function to achieve optimal performance:  
  
    - Before you post a general journal batch, you must check all journal lines for validity and verify that all lines balance. After the first line when an error is found, you don't have to retrieve the rest of the rows.  
  
    - if you want to fulfill multiple outstanding orders from a recent purchase but you don't know how many orders are covered by the purchase.  
  
- **Record.FindSet(ForUpdate, UpdateKey)** is optimized for reading the complete set of records in the specified filter and range. The *UpdateKey* parameter doesn't influence the efficiency of this method in [!INCLUDE[prod_long](../developer/includes/prod_long.md)], such as it did in [!INCLUDE[nav2009](../developer/includes/nav2009_md.md)].  
  
     FindSet isn't implemented by issuing a TOP X call.  
  
- **Record.FindFirst** and **Record.FindLast** are optimized for finding the single first or last record in the specified filter and range.  
  
- **Record.Next** can be called at any time. However, if **Record.Next** isn't called as part of retrieving a continuous result set, then [!INCLUDE[prod_short](../developer/includes/prod_short.md)] calls a separate SQL statement in order to Find the Next record.  
  
### Dynamic result sets  

Any result set that is returned from a call to the Find methods discussed in the previous section is dynamic. That means that the result set is guaranteed to contain any changes that you make further ahead in the result set. However, this feature comes at a cost. If any modifications are made to a table being traversed, then [!INCLUDE[prod_short](../developer/includes/prod_short.md)] might have to issue an extra SQL statement to guarantee that the result set is dynamic.  
  
The following code shows how records are most efficiently retrieved. **FindSet** is the most efficient method to use because this example reads all records.  
  
```AL  
if FindSet() then  
    repeat  
        // Insert statements to repeat.  
    until Next() = 0;  
```  
  
## <a name="calc"></a>CalcFields, CalcSums, and Count  

Each call to the **CalcFields**, **CalcField**, **CalcSum**, or **CalcSums** methods requires a separate SQL statement unless the client calculates the same sum or another sum using the same SumIndexFields or filters in a recent operation. In that case, the result is cached.
  
Each **CalcFields** or **CalcSums** request should be confined to use only one SIFT index. The SIFT index can only be used if:  
  
- All requested sum-fields are contained in the same SIFT index.  
  
- The filtered fields are part of the key fields specified in the SIFT index containing all the sum fields.  
  
If neither of these requirements is fulfilled, then the sum is calculated directly from the base table.  

In [!INCLUDE[prod_long](../developer/includes/prod_long.md)], SIFT indexes count records in a filter if a SIFT index contains all filtered fields in the key fields defined for the SIFT index.
  
## SetAutoCalcFields  

It's a common task to retrieve data and request calculation of associated FlowFields. The following example traverses customer records, calculates the balance, and marks the customer as blocked if the customer exceeds the maximum credit limit. **Note:** the Customer record and associated fields are *imaginary* in the following examples.  

> [!NOTE]
> Starting in [!INCLUDE[prod_short](../includes/prod_short.md)] [!INCLUDE[2025_releasewave1_name](../includes/2025_releasewave1_name.md)], the **SetAutoCalcFields** is also available on the **RecordRef** data type.

```AL  
if Customer.FindSet() then 
    repeat  
        Customer.CalcFields(Balance);  
        if Customer.Balance > MaxCreditLimit then begin  
            Customer.Blocked := true;   
            Customer.Modify();  
        end else 
            if Customer.Balance > LargeCredit then begin 
                Customer.Caution := true;  
                Customer.Modify();   
            end;   
    until Customer.Next() = 0;  
```  
  
In [!INCLUDE[prod_long](../developer/includes/prod_long.md)], you can do this operation faster. First, we set a filter on the customer. In [!INCLUDE[prod_long](../developer/includes/prod_long.md)], setting a filter on a record is translated into a single SQL statement.  
  
```AL  
Customer.SetFilter(Balance, '>%1' , LargeCredit);   
if Customer.FindSet() then 
    repeat  
        Customer.CalcFields(Balance);  
        if Customer.Balance > MaxCreditLimit then begin   
            Customer.Blocked := true;   
            Customer.Modify();   
        end else 
            if Customer.Balance > LargeCredit then begin   
                Customer.Caution := true;   
                Customer.Modify();   
            end;   
    until Customer.Next() = 0;   
```  
  
In the previous example, an extra call to CalcFields still must be issued for the code to be able to check the value of Customer.Balance. In [!INCLUDE[prod_long](../developer/includes/prod_long.md)], you can optimize this further by using the new **SetAutoCalcFields** method.  
  
```AL  
Customer.SetFilter(Balance, '>%1', LargeCredit);   
Customer.SetAutoCalcFields(Balance)   
if Customer.FindSet() then 
    repeat   
        if Customer.Balance > MaxCreditLimit then begin   
            Customer.Blocked := true;   
            Customer.Modify();   
        end else 
            if Customer.Balance > LargeCredit then begin   
                Customer.Caution := true;   
                Customer.Modify();   
            end;   
    until Customer.Next() = 0;  
```  
  
## Insert, Modify, Delete, and LockTable

Each call to **Insert**, **Modify**, or **Delete** methods requires a separate SQL statement. If the table that you Modify contains SumIndexes, then the operations are slower. As a test, select a table that contains SumIndexes and execute 100 **Insert**, **Modify**, or **Delete** operations to measure how long it takes to maintain the table and all its SumIndexes.  

Cloning a record before a **Modify** or **Delete** operation issues an extra SQL statement, since the SQL `SELECT` query is restarted every time the table is cloned. A record is cloned when calling the [Copy Method](../developer/methods-auto/record/record-copy-method.md), when using a **RecordRef** or when the record isn't passed with the `var` parameter within a function.

The following code samples lead to a bad performance, since they'll issue an extra SQL statement per record in the table.

```AL
if MyTable.FindSet() then
    repeat
        MyTableCopy.Copy(MyTable);
        // ...
        MyTableCopy.Modify(); // or .Delete();
    until MyTable.Next() = 0;
```

```AL
if MyTable.FindSet() then
    repeat
        RecRef.GetTable(MyTable);
        // ...
        RecRef.Modify(); // or .Delete();
    until MyTable.Next() = 0;
```

Instead, you should use the following code, which only requires an extra SQL statement:

```AL
RecRef.Open(Database::"My Table");
if RecRef.FindSet(true) then
    repeat
        // ...
        RecRef.Modify(); // or .Delete();
    until RecRef.Next() = 0;
```

```AL
if MyTable.FindSet(true) then
    repeat
        MyTable.Modify(); // or .Delete();
    until MyTable.Next() = 0;
```

The **LockTable** method doesn't require any separate SQL statements. It causes any subsequent reading from any tables to be done with an update lock. For more information, see [Record.LockTable Method](../developer/methods-auto/record/record-locktable-method.md). 

## ModifyAll and DeleteAll

Using **ModifyAll** and **DeleteAll** can improve performance by limiting the amount of SQL calls needed. However, be aware that  **ModifyAll** and **DeleteAll** revert to individual calls if any of the following conditions exist:

- There's trigger code on the table.
- There are event subscribers to the following events: OnBeforeModify, OnAfterModify, OnGlobalModify, OnBeforeDelete, OnAfterDelete, OnGlobalDelete, and OnDatabaseModify.
- Security filtering is active.
- The table contains `Media` or `MediaSet` data type fields.
- There are fields that are added through companion tables.
  
## Related information  

[Table Keys and Performance](optimize-sql-table-keys-and-performance.md)   
[Bulk Inserts](optimize-sql-bulk-Inserts.md)   
[Get Method](../developer/methods-auto/record/record-get-method.md)   
[Find Method](../developer/methods-auto/record/record-Find-method.md)  
[Next Method](../developer/methods-auto/record/record-Next-method.md)  
[FindSet Method](../developer/methods-auto/record/record-FindSet-method.md)   
[FindFirst Method](../developer/methods-auto/record/record-FindFIRST-method.md)   
[FindLast Method](../developer/methods-auto/record/record-FindLAST-method.md)   
[CalcFields Method](../developer/methods-auto/record/record-CalcFields-method.md)   
[CalcField Method](../developer/methods-auto/fieldref/fieldref-CALCFIELD-Method.md)   
[CalcSums Method](../developer/methods-auto/record/record-CalcSums-method.md)   
[CalcSum Method](../developer/methods-auto/fieldref/fieldref-CALCSUM-Method.md)   
[SetAutoCalcFields Method](../developer/methods-auto/record/record-SETAUTOCalcFields-method.md)  
[Insert Method](../developer/methods-auto/record/record-Insert--method.md)   
[Modify Method](../developer/methods-auto/record/record-Modify-method.md)  
[ModifyAll Method](../developer/methods-auto/record/record-ModifyAll-method.md)     
[Delete Method](../developer/methods-auto/record/record-Delete-method.md)  
[DeleteAll Method](../developer/methods-auto/record/record-DeleteALL-method.md)   
[LockTable Method](../developer/methods-auto/record/record-LOCKTABLE-method.md)  
[Events in AL](../developer/devenv-events-in-al.md)  
[Using Security Filters](../security/security-filters.md)
