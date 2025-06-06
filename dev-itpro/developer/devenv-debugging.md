---
title: Debugging in AL
description: Debugging in AL with Visual Studio Code and the AL Language extension.
author: SusanneWindfeldPedersen
ms.custom: bap-template
ms.date: 01/20/2025
ms.reviewer: solsen
ms.topic: concept-article
ms.author: solsen
ms.collection: get-started
---

# Debugging in AL

[!INCLUDE[azure-ad-to-microsoft-entra-id](~/../shared-content/shared/azure-ad-to-microsoft-entra-id.md)]

[!INCLUDE [getstarted-contributions](includes/getstarted-contributions.md)]

*Debugging* is the process of finding and correcting errors. With Visual Studio Code and the [!INCLUDE[d365al_ext_md](../includes/d365al_ext_md.md)], you get an integrated debugger to help you inspect your code and verify that your application can run as expected. You can start a debugging session by pressing <kbd>F5</kbd>. For more information about Debugging in Visual Studio Code, see [Debugging](https://code.visualstudio.com/docs/editor/debugging). 

An alternative to classic debugging is snapshot debugging, which allows you to record running code, and later debug it. Learn more in [Snapshot debugging](devenv-snapshot-debugging.md).

> [!IMPORTANT]  
> To enable debugging in versions prior to [!INCLUDE[prod_short](../includes/prod_short.md)] April 2019, the `NetFx40_LegacySecurityPolicy` setting in the Microsoft.Dynamics.Nav.Server.exe.config file must be set to **false**. This step requires a server restart.

> [!IMPORTANT]  
> To use the development environment and debugger for on-premises environments, you must make sure that port `7049` is available.

## Limitations to be aware of

- "External code" can only be debugged if the code has the `allowDebugging` flag set to `true`. Learn more in [Resource exposure policy setting](devenv-security-settings-and-ip-protection.md). 
- The debugger launches a new client instance each time you select <kbd>F5</kbd>. If you close the debugging session and then start a new session, this new session relies on a new client instance. We recommend that you close the Web client instances when you close a debugging session.
- Pausing the debugging session isn't supported.

To control table data synchronization between each debugging session, see [Retaining table data after publishing](devenv-retaining-data-after-publishing.md).  

> [!TIP]  
> To be able to debug an online environment with an Embed app published in it, make sure to specify the `applicationFamily` parameter in your launch.json file. You must define the application family for your Embed app during onboarding. 

## Breakpoints
  
The basic concept in debugging is the *breakpoint*, which is a mark that you set on a statement. When the program flow reaches the breakpoint, the debugger stops execution until you instruct it to continue. Without any breakpoints, the code runs without interruption when the debugger is active. You can set a breakpoint by using the **Debug Menu** in Visual Studio Code. For more information, see [Debugging shortcuts](#debugging-shortcuts). 
 
Set breakpoints on the external code that isn't part of your original project. You can step into the base application code by using the **Go to Definition** feature and set breakpoints on the referenced code, which is generally a `.dal` file. To set a breakpoint on the external code or base application code, you do as follows: 

- Use **Go to Definition**, which opens the "external file", and then a breakpoint can be set.  
- By using the debugger, you can step into the code, and then set a breakpoint.

The following video illustrates that `Customer.dal` is an external file. A breakpoint is set in the `Customer.dal` file, which is referenced from your AL project to stop execution at the marked point. 

![Debugger.](media/DebuggingAL.gif)

Learn more about **Go to Definition** in [AL code navigation](devenv-al-code-navigation.md). 

### Conditional breakpoints

You can also set a condition on a breakpoint and if the condition evaluates as true, then code execution breaks at the breakpoint. Learn more in [Setting conditional breakpoints](devenv-debugging-conditional-breakpoints.md).

## Break on errors

Specify if the debugger breaks on the next error by using the `breakOnError` property. If the debugger is set to `breakOnError`, it stops execution on both errors that are handled in code and unhandled errors.

The default value of the `breakOnError` property is **true**, which means the debugger stops the execution that throws an error by default. To skip the error handling process, set the `breakOnError` property to **false** in the `launch.json` file. 

> [!TIP]  
> If the debugging session takes longer, you can refresh the session by selecting the <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> keys and selecting **Reload Window**.

## Break on record changes

Specify if the debugger breaks on record changes by using the `breakOnRecordWrite` property. If the debugger is set to break on record changes, then it breaks before creating, modifying, or deleting a record. The following table shows each record change and the AL methods that cause each change. 

|Record change|AL Methods|  
|-------------------|---------------------|  
|Create a new record|[Insert method (Record)](methods-auto/record/record-insert--method.md)|  
|Update an existing record|[Modify method (Record)](methods-auto/record/record-modify-method.md), [ModifyAll method (Record)](methods-auto/record/record-modifyall-method.md), [Rename method (Record)](methods-auto/record/record-rename-method.md)|  
|Delete an existing record|[Delete method (Record)](methods-auto/record/record-delete-method.md), [DeleteAll method (Record)](methods-auto/record/record-deleteall-method.md)|  


The default value of the `breakOnRecordWrite` property is **false**, which means that the debugger isn't set to break on record changes by default. To break on record changes, you can set the `breakOnRecordWrite` property to **true** in the `launch.json` file. Learn more about the [Launch JSON File](devenv-json-launch-file.md).

## Debugging large size variable values

Variables with values larger than 1024 bytes are truncated (`…`) and can't be fully inspected from the **VARIABLES** window. To inspect a large size variable value, instead use the **DEBUG CONSOLE** and write the name, or qualified name of a variable to inspect at the prompt and then select <kbd>Enter</kbd>.

## Attach and debug next

If you don't want to publish and invoke the functionality to debug it, you can attach a session to a specified server and await a process to trigger the breakpoint you have set. This is useful when you want to debug a specific process, such as a web service call. Learn more in [Attach and debug next](devenv-attach-debug-next.md).

## Debugging shortcuts

|Keystroke    |Action         |
|-------------|---------------|
|<kbd>F5</kbd>           |Start debugging|
|<kbd>Ctrl</kbd>+<kbd>F5</kbd>       |Start without debugging|
|<kbd>Shift</kbd>+<kbd>F5</kbd>     |Stop debugging|
|<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F5</kbd>|Start debugging without publishing. <br> Using this command on a changed but unpublished code might trigger false existing breakpoints. For example, if you modify the method "foo", add two lines, put a breakpoint on the second line, and then start debugging without publishing, that breakpoint won't be hit, or if it's hit it isn't your new code that it breaks. If it breaks, it breaks on the line that the server thinks the breakpoint is, based on the last published code.|
|<kbd>Alt</kbd>+<kbd>F5</kbd>       |Start RAD with debugging. Learn more in [Working with Rapid Application Development](devenv-rad-publishing.md).|
|<kbd>F10</kbd>         |Step over|
|<kbd>F11</kbd>          |Step into|
|<kbd>Shift</kbd>+<kbd>F11</kbd>    |Step out|
|<kbd>F12</kbd>          |Go To Definition| 

Learn more about shortcuts in [Debugging in Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). Learn more about working with Snapshot debugging in [Snapshot debugging](devenv-snapshot-debugging.md).

<!-- 
To use the Go To Definition on local server, it requires that the AL symbols are rebuilt and downloaded from C/SIDE. The application symbols that were built with the previous version of C/SIDE would not make it possible to have Go To Definition work on base application methods. -->

## <a name="DebugSQL"></a>Debugging SQL behavior

Traditionally, debugging AL has been about examining the behavior of the language runtime, for example, looking into the content of local variables at a breakpoint. As of [!INCLUDE[prod_short](includes/prod_short.md)] April 2019, the AL debugger also offers the capability to examine your AL code's effect on the [!INCLUDE[prod_short](includes/prod_short.md)] database. The `enableSQLInformationDebugger` setting enables this functionality. Learn more about these properties in the [Launch JSON file](devenv-json-launch-file.md).

### View database statistics

In the **VARIABLES** pane in debugger, expand the **\<Database statistics\>** node to get insights such as the current network latency between the [!INCLUDE[server](includes/server.md)] and the [!INCLUDE[prod_short](includes/prod_short.md)] database, the total number of SQL statements executed, the total number of rows read, and locks held, as well as insights into the most recent SQL statements executed by the server. The following insights are part of the database statistics:

|Insight | Description  |
|-------|-------|
|Current SQL latency (ms) | When the debugger hits a breakpoint, the [!INCLUDE[server](includes/server.md)] sends a short SQL statement to the database, and measures the time it takes. The value is in milliseconds.| 
|Number of SQL Executes | This number shows the total number of SQL statements executed in the debugging session since the debugger was started.|
|Number of SQL Rows Read | This number shows the total number of rows read from the [!INCLUDE[prod_short](includes/prod_short.md)] database in the debugging session since the debugger was started.|

> [!TIP]
> You can also get database insights from the AL runtime by using the [SqlStatementsExecuted()](methods-auto/sessioninformation/sessioninformation-sqlstatementsexecuted-method.md) and [SqlRowsRead()](methods-auto/sessioninformation/sessioninformation-sqlrowsread-method.md) methods.

### View locks held

The Locks part of the database statistics shows an overview of the SQL locks held by the debugged session. The insight can be used to understand the locks acquired while stepping through AL code. The access mode of the lock is also provided, which allows advanced developers to establish concurrency compatibility with other operations.

### View SQL statement statistics

The database insights also let you peek into the most recent and the latest long running SQL statements executed by the server. To view a list of the statements, expand either the **\<Last Executed SQL Statements\>** or **\<Last Long Running SQL Statements\>** node. The following insights are part of the SQL statement statistics:

| Insight    | Description      |
|-------|-------|
|Statement | The SQL statement that the AL server sent to the [!INCLUDE[prod_short](includes/prod_short.md)] database. For further analysis, you can copy the SQL statement into other database tools, such as SQL Server Management Studio.| 
|Execution time (UTC) | The timestamp (in UTC) of when the SQL statement was executed. You can use this to infer whether the SQL statement was part of the AL code between the current and last breakpoint (if set).
|Duration (ms) | The duration in milliseconds of the total execution time of the SQL statement measured inside the [!INCLUDE[server](includes/server.md)]. You can use Duration (ms) to analyze whether you're missing indexes ([!INCLUDE[prod_short](includes/prod_short.md)] keys), or to experiment with database partitioning and/or compression performance.|
|Approx. Rows Read | This number shows the approximate number of rows read from the [!INCLUDE[prod_short](includes/prod_short.md)] database by the SQL statement. You can use this insight to analyze whether you're missing filters.|

The number of SQL statements tracked by the debugger can be configured in the [!INCLUDE[server](includes/server.md)]. The default value is 10.

> [!NOTE]  
> For [!INCLUDE[prod_short](includes/prod_short.md)] on-premises, the [!INCLUDE[server](includes/server.md)] instance has several configuration settings that control the SQL statistics. These statistics are gathered and then displayed in the debugger, like whether long running SQL statements or SQL statements are shown. Check the server configuration if you don't see the insights that you expect in the debugger. Learn more in [Configuring Business Central server](../administration/configure-server-instance.md#Development).

## Debugging web services

It's possible to debug code executed from web service endpoints, both pages and codeunits exposed as OData/SOAP, and API pages/queries. To do so, set the `breakOnNext` setting to `WebServiceClient` and trigger the web service endpoint from an API explorer tool or your web service client code. Learn more in [Attach and debug next](devenv-attach-debug-next.md).

## NonDebuggable attribute

The ability to debug certain methods and/or variables can be restricted. Learn more in [NonDebuggable attribute](attributes/devenv-nondebuggable-attribute.md).

## Authenticate with Microsoft Entra ID on Business Central on-premises

You can use Microsoft Entra ID as the authentication mechanism for [!INCLUDE[prod_short](includes/prod_short.md)] on-premises or containers. Learn more in [Microsoft Entra authentication for Business Central on-premises](devenv-aad-auth-onprem.md).

## Troubleshooting your debugging setup

This section provides some tips and tricks for working with and troubleshooting your debugging setup.

### Debugging in versions prior to [!INCLUDE[prod_short](../includes/prod_short.md)] April 2019

To enable debugging in versions prior to [!INCLUDE[prod_short](../includes/prod_short.md)] April 2019, the `NetFx40_LegacySecurityPolicy` setting in the Microsoft.Dynamics.Nav.Server.exe.config file must be set to **false**. This step requires a server restart.

### Firewall settings for on-premises environments (port 7049)

To use the development environment and debugger for on-premises environments, you must make sure that port `7049` (the default port for the debugger) is open. The port can be changed with the server setting ``DeveloperServicesPort``.

### Debug an online environment with an Embed app published in it

To be able to debug an online environment with an Embed app published in it, make sure to specify the `applicationFamily` parameter in your launch.json file. You must define the application family for your Embed app during onboarding. 

### Launching debug sessions to on-premises environments

To be able to debug sessions in an on-premises environment, make sure to specify the `usePublicURLFromServer` parameter in your launch.json file. Learn more in  [Publish to local server settings (launch.json)](devenv-json-launch-file.md#publish-to-local-server-settings-launchjson)


## Related information

[Attach and debug next](devenv-attach-debug-next.md)  
[Snapshot debugging](devenv-snapshot-debugging.md)  
[Developing extensions](devenv-dev-overview.md)  
[JSON files](devenv-json-files.md)  
[AL code navigation](devenv-al-code-navigation.md)  
