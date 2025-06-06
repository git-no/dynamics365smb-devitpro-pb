---
title: "Code Conversion from C/AL to AL"
description: "Description of the conversion process from C/AL to AL."
ms.date: 04/01/2021
ms.topic: how-to
author: jswymer
ms.author: jswymer
ms.reviewer: jswymer
---
# Code Conversion from C/AL to AL

This article explains how to convert a [!INCLUDE[prod_short](../developer/includes/prod_short.md)] (version 14) C/AL code-customized on-premises solution to AL code.

You'll use this procedure as part of the upgrade process when going from version 14 to a later version.

## Before you start

Get familiar with the basics of setting up and developing in Visual Studio Code and AL, see [Developing Extensions in AL](../developer/devenv-dev-overview.md).

> [!NOTE]  
> Moving on-premise C/AL code customizations to [!INCLUDE[d365fin_long_md](../developer/includes/d365fin_long_md.md)] online, requires converting these to AL extensions. This could include converting the C/AL deltas to AL extension code as a starting point, as outlined in [The Txt2Al Conversion Tool](../developer/devenv-txt2al-tool.md).

Also, review the [Known Issues](known-issues.md) for information about issues that may affect the upgrade.

#### Breaking changes

When converting from C/AL to AL, it's important that you don't introduce any breaking schema changes to the database. Otherwise, you won't be able to synchronize the new extension with the database.

## Task 1: Import the test library into your C/AL solution 

If your solution uses Microsoft (1st-party) extensions, you'll have to convert the test library from C/AL to AL. The reason for this is that the Microsoft extensions rely on the test symbols. The easiest way is to import the **CALTestLibraries.W1.fob** file into the old database. This file is available on the version 14 installation media (DVD) in the **TestToolKit** folder.

You can do this using the ([!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)]). For more information, see [Exporting and Importing Objects](../cside/cside-import-objects.md).

## Task 2: Compile all the objects in your C/AL solution

Compiling all the objects is a prerequisite for a successful and complete export. To compile objects, you can use either of the following tools:  
- C/SIDE ([!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)]). See [Compiling Objects](../cside/cside-compiling-objects.md).
- [Compile-NAVApplicationObject](/powershell/module/microsoft.dynamics.nav.ide/compile-navapplicationobject) cmdlet of the [!INCLUDE[devshell](../developer/includes/devshell.md)]. Make sure to run this cmdlet as an administrator.

## Task 3: Export the application objects to the new TXT syntax

Once the application compiles, you must export all C/AL application objects, except system tables and codeunits (IDs in the 2000000000 range), to the new TXT format. The exported objects will be used as input to the Txt2AL conversion tool. To export objects, use the [Export-NAVApplicationObject](/powershell/module/microsoft.dynamics.nav.ide/export-navapplicationobject) cmdlet of the [!INCLUDE[devshell](../developer/includes/devshell.md)]. it's important to:

- Omit all system objects, which have IDs in the 2000000000 range. 
- Use the `ExportToNewSyntax` switch to export the objects in a syntax that is compatible with the Txt2Al conversion tool.

The Export-NAVApplicationObject cmdlet will export all objects to a single .txt file. If you imported the test library objects into the database, then you'll export the base application objects and the test library separately. Later, you'll create a separate AL project for each set of files.

For example, do the following steps:

1. Export the custom base application objects.
    1. Create a folder for storing the exported base application objects to TXT files (for example, c:\export2al\baseapplication).
    2. Run the following commands to export tha application objects, but omitting the system objects and test library objects.

        ```ps
        Export-NAVApplicationObject -DatabaseServer .\BCDEMO -DatabaseName "Demo Database BC (14-0)" -ExportToNewSyntax -Path "c:\export2al\baseapplication\exportedbc14app-part1.txt" -Filter 'Id=1..129999'
        ```
    
        ```
        Export-NAVApplicationObject -DatabaseServer .\BCDEMO -DatabaseName "Demo Database BC (14-0)" -ExportToNewSyntax -Path "c:\export2al\baseapplication\exportedbc14app-part2.txt" -Filter 'Id=140000..1999999999'
        ```
    
        ```
        Export-NAVApplicationObject -DatabaseServer .\BCDEMO -DatabaseName "Demo Database BC (14-0)" -ExportToNewSyntax -Path "c:\export2al\baseapplication\exportedbc14testobjects.txt" -Filter 'Id=130400..130416'
        ```
2. Export the test library objects.
    1. Create a folder for storing the exported test library objects to TXT files (for example, c:\export2al\testlibrary).
    2. Run the following commands to export the test library objects only
    
        ```
        Export-NAVApplicationObject -DatabaseServer .\BCDEMO -DatabaseName "Demo Database BC (14-0)" -ExportToNewSyntax -Path "c:\export2al\testlibrary\bc14testlibrary-part1.txt" -Filter 'Id=130000..130399'
        ```
    
        ```
        Export-NAVApplicationObject -DatabaseServer .\BCDEMO -DatabaseName "Demo Database BC (14-0)" -ExportToNewSyntax -Path "c:\export2al\testlibrary\bc14testlibrary-part2.txt" -Filter 'Id=130440..139999'
        ```

## Task 4: Create a declaration file for custom .NET assemblies (optional)

If your solution contains .NET interoperability code and control add-ins, you can create a file that contains the declarations to the assemblies. This file will be used when you convert the C/AL TXT files to AL in the next step. Alternatively, after the conversion, you'll have to manually add the declarations to objects that use the assemblies.

To create the file, use a text editor or Visual Studio code to create a file that contains the assembly declarations as follows:

``` 
dotnet 

{ 
    assembly("Microsoft.Dynamics.Nav.Client.BusinessChart") 
    { 
        type("Microsoft.Dynamics.Nav.Client.BusinessChart.BusinessChartAddIn";"Microsoft.Dynamics.Nav.Client.BusinessChart") 
        { 
            IsControlAddIn = true; 
        } 

        // Other control add-ins in this assembly 
    } 

    // Other assemblies containing control add ins 
}  

```

Save the file with any name and the extension **\.al**, for example **mydotnet.al**. Make a note of the path because you'll use it in the next step. 

## Task 5: Convert the C/AL TXT files to AL

With C/AL exported to the new TXT format, you now convert the code to AL using the [The Txt2Al Conversion Tool](../developer/devenv-txt2al-tool.md). The Txt2Al creates \.al files for each object in the TXT files. Similar to **Task 3**, if you imported the test library objects into the database, then you'll convert the base application objects and the test library separately.

#### Get the Txt2AL conversion tool

The Txt2Al conversion tool (txt2al.exe) is only available with version 14, which is the last version to support C/AL. Use this version of the tool no matter what later version you may eventually be upgrading to. The AL objects created by the tool will be compatible with later versions.

You find the txt2al.exe on the installation media (DVD) in the "DVD\RoleTailoredClient\program files\Microsoft Dynamics NAV\140\RoleTailored Client" folder. Or, it's installed locally with [!INCLUDE[nav_dev_long_md](../developer/includes/nav_dev_long_md.md)], for example, in the "C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\140\RoleTailored Client" folder.

#### Run the Txt2AL conversion tool

1. Convert the base application TXT files to AL.
    1. Create a folder for storing the AL files for base application objects (for example, c:\export2al\baseapplication\al).
    2. Start a command prompt as administrator, and navigate to the folder that contain txt2al.exe file.

        By default, the location is C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\140\RoleTailored Client.
    3. Run the txt2al command:

        ```
        txt2al --source=C:\export2al\baseapplication --target=C:\export2al\baseapplication\al --injectDotNetAddIns --dotNetAddInsPackage=C:\export2al\dotnet\mydotnet.al --dotNetTypePrefix=BC --rename
        ```

        If your solution contains .NET interoperability code, the following Txt2Al command line parameters are used to achieve a conversion that requires less manual intervention:  

        - `--injectDotNetAddIns` injects the definition of standard .NET add-ins in the resulting .NET package. The standard .NET add-ins are a set of add-ins that are embedded into the platform.
        - `--dotNetAddInsPackage` should be used to point the conversion tool to an AL file containing declarations for the .NET types that represent .NET control addins. Use this to inject a custom set of .NET control add-in declarations. This parameter is only required if you completed **Task 4**, and you set it to point to the location of the dotnet.al file.

            > [!NOTE]
            >If you are interested in migrating your localization resources, you should use the `--addLegacyTranslationInfo` switch to instruct Txt2Al to generate information about the legacy IDs of the translation code.
        - `--dotNetTypePrefix` specifies a prefix to be used for all .NET type aliases created during the conversion. This will ensure that no naming conflicts occur with existing types. In the example, `BC` is the prefix.

        - `--rename` renames the output files to prevent clashes with the source .txt files.

        When completed, there will be an \.al file for each object.

2. Convert the test library TXT files to AL.

    This is similar to the previous step.
    1. Create a folder for storing the AL files for test objects (for example, c:\export2al\testlibrary\al).
    2. Run the txt2al command:

        ```      
        txt2al --source=C:\export2al\testlibrary --target=C:\export2al\testlibrary\al --injectDotNetAddIns --dotNetTypePrefix=BCTest --rename
        ```

        Use a different value for the `--dotNetTypePrefix` than you did for the base application.
<!--
## Task 6: Copy CodeViewer add-in to the version 15.0 server installation

Copy the **CodeViewer** folder from the **Add-ins** folder of the Business Central version 14 RoleTailored client installation (C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\140\RoleTailored Client\Add-ins) to the **Add-ins** folder of the Business Central 150 Server installation (C:\Program Files\Microsoft Dynamics 365 Business Central\150\Service\Add-ins). Replace the existing folder and files, if any.
 
In version 15.0 CodeViewer is no longer used, but it's required because of references thar exist in the converted application. If you omit this step, you might get compilation errors.
-->
## Task 6: Create a new application database for development

To build your base application, you'll create a new application database on the Business Central platform version that you're upgrading to (like 18, 19, or 20). This will only be used during development.

1. Start the [!INCLUDE[adminshell](../developer/includes/adminshell.md)] for new version as an administrator.
2. Run the New-NAVApplicationDatabase cmdlet to create the database. For example:

    ```
    New-NAVApplicationDatabase -DatabaseServer .\BCDEMO -DatabaseName MyDBforupgrade
    ```

3. Connect your [!INCLUDE[server](../developer/includes/server.md)] instance to the database. See [Connecting a Business Central Server Instance to a Database](../administration/connect-server-to-database.md).

    ```
    Set-NAVServerConfiguration -ServerInstance BC -KeyName DatabaseName -KeyValue "MyDBforupgrade"
    ```

4. Restart the server instance.

    ```
    Restart-NAVServerInstance -ServerInstance BC
    ```

<!--
5. Publish system symbols extension (System.app) to application on the server instance. 

    The System.app file is located where you installed the AL Development Environment, which by default is the C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\150\AL Development Environment folder. This package contains the symbols for all the system tables and codeunits.

    Start [!INCLUDE[adminshell](../developer/includes/adminshell.md)] for version 15.0 as an administrator, and run the Publish-NAVApp cmdlet:

    ```
    Publish-NAVApp -ServerInstance BC150 -Path "C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\150\AL Development Environment\System.app" -PackageType SymbolsOnly
    ```
-->

## <a name="build"></a>Task 7: Create and build an AL project for custom base application

In this task, you'll create an AL project in Visual Studio code that you'll use for building your custom base application extension based on your converted C/AL application.

1. If you haven't already, install Visual Studio Code and the latest AL Language extension for version 18, 19, or 20 as outlined in [Get Started with AL](../developer/devenv-get-started.md).

2. Configure Visual Studio Code for optimal performance with AL projects.

    This step is optional, but recommended. For more information, see [Optimize Visual Studio Code for Editing and Building](../developer/devenv-optimize-visual-studio-code.md).

3. In Visual Studio Code, from the **Command Palette**, select the **AL Go!** command to create a new project.

    Specify the path for the project, and set the **Target Platform** to version you're upgrading to. When prompted to select your server, choose <!--Microsoft cloud sandbox or--> **Your own server**.
4. Create a **\.alpackages** folder in the root folder of the project and then copy the system (platform) symbols extension (System.app file) to the folder.

    The System.app file is located where you installed the AL Development Environment, which by default is the C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\<180, 190, 200>\AL Development Environment folder. This package contains the symbols for all the system tables and codeunits.
5. Delete the **HelloWorld.al** sample file from the project.

6. Modify the `settings.json` file of Visual Studio Code to configure the assembly probing path.

    Change `"al.assemblyProbingPaths": ["./.netpackages"]` to point to all the folders that contain .NET assemblies that are used by your project. Here is an example that contains the most typical paths:

    ```json
    "al.assemblyProbingPaths": [
    "C:\\Program Files\\Microsoft Dynamics 365 Business Central\\200\\Service",
    "C:\\Program Files (x86)\\Microsoft Dynamics 365 Business Central\\200\\RoleTailored Client",
    "C:\\Program Files (x86)\\Reference Assemblies\\Microsoft\\Framework\\.NETFramework\\v4.8",
    "C:\\Program Files (x86)\\Reference Assemblies\\Microsoft\\WindowsPowerShell"
    ]
    ```

    Replace 200 with the value that matches your installation, like 180 or 190.

    For more information about the settings.json, see [User and Workspace Settings](https://code.visualstudio.com/docs/getstarted/settings).

    > [!NOTE]  
    > Adding assemblies to the folders in your assembly probing paths is not automatically detected by the compiler. You must restart Visual Studio Code for the changes to be detected.

7. Modify the `app.json` for the project as follows:

    |Parameter|Value|
    |---------|-----|
    |`"id":`|Use the default that was created for you.|
    |`"name":`|Specify any valid name.|
    |`"publisher":`|Specify any valid publisher name.|
    |`"version":`|You can use any valid version number, but it's recommended to set it to the same version as the C/AL application.|
    |`"dependencies":`|Delete all values, so this parameter is empty.|
    |`"platform":`|Set to match the platform version that you're upgrading to: <ul><li>`"20.0.0.0"`- version 20</li><li>`"19.0.0.0"`- version 19</li><li>`"18.0.0.0"`- version 18</li></ul>|
    |`"application":`|Remove this parameter.|
    |`"idRange":`|Set to include all the IDs used by your base application, or leave it blank.|
    |`"runtime":` |Set to match the version that you're upgrading to:<ul><li>`"9.0"`- version 20</li><li>`"8.0"`- version 19</li><li>`"7.0"`- version 18</li></ul>|
    |`"target":"`|Add this parameter and set to `"OnPrem"`.|

    For example:

    ```json
    {
        "id": "00001111-aaaa-2222-bbbb-3333cccc4444",
        "name": "My Base Application",
        "publisher": "Microsoft",
        "version": "14.13.0.0",
        "brief": "",
        "description": "",
        "privacyStatement": "",
        "EULA": "",
        "help": "",
        "url": "",
        "logo": "",
        "dependencies": [],
        "screenshots": [],
        "platform": "19.0.0.0",
        "idRanges": [  ],
        "contextSensitiveHelpUrl": "https://BC14cal-to-18al-app.com/help/",
        "showMyCode": true,
        "runtime": "8.0",
        "target":  "OnPrem"
    }  
    ```

8. Copy all of the base application AL files generated in the previous task (**Task 5**) to the root folder of your project.

9. Open the **dotnet.al** file for the project, and make the following changes:

    - Delete all instances of `Version = '14.0.0.0';` for **Microsoft.Dynamics.Nav** assembly declarations.
    - For the `DocumentFormat.OpenXml` assembly declaration, remove the `version` and `culture` keys and set `PublicKeyToken = '8fb06cb64d019a17'`.

        ```
        assembly("DocumentFormat.OpenXml")
        {
            PublicKeyToken = '8fb06cb64d019a17';
            ...
        ```

10. Delete objects that are related to the client debugger client.

     Debugging from the client has been discontinued, and replaced by AL Debugger. The version 14 debugger objects aren't supported on later versions. To avoid compilation errors, delete the following objects:
    
    - Debugger.Page.al
    - DebuggerBreakpointCondition.Page.al
    - DebuggerBreakpointList.Page.al
    <!-- - DebuggerBreakRules.Page.al-->
    - DebuggerCallstackFactBox.Page.al
    - DebuggerCodeViewer.Page.al
    - DebuggerManagement.Codeunit.al
    - DebuggerVariableList.Page.al
    - DebuggerWatchValueFactBox.Page.al
    - SessionList.Page.al

    You might also have to remove references to `SessionList` in ChangeGlobalDimensions.Codeunit.al.
    
11. Build and compile your project (select <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>B</kbd>).

    The AL compiler will issue errors for constructs that aren't valid. Fix any errors that occur, and build again.

    > [!TIP]
    > If you are maintaining your C/AL solution going forward, we recommend that you fix errors in C/AL objects and convert to AL again. This makes it future changes easier to forward push changes because code bases will be similar.
    <!--
    The following are known issues that you might encounter:
    1. FlowSelectorTemplate.Page.al
        
        ```   
        usercontrol(FlowAddin;"Microsoft.Dynamics.Nav.Client.FlowIntegration")
        {
        ApplicationArea = Basic,Suite;
    
        trigger ControlAddInReady()
        begin
        /*                                 CurrPage.FlowAddin.Initialize(
            FlowServiceManagement.GetFlowUrl,FlowServiceManagement.GetLocale,
            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetFlowARMResourceUrl,FlowServiceManagement.GetFlowResourceName,false),
            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetAzureADGraphhResourceUrl,FlowServiceManagement.GetFlowResourceName,false),
            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetMicrosoftGraphhResourceUrl,FlowServiceManagement.GetFlowResourceName,false));
    
        LoadTemplates;
    
        AddInReady := true; */
        end;
        ```   
    
    2. FlowSelector.Page.al
        
        ```   
            group(Control3)
            {
                ShowCaption = false;
                Visible = IsUserReadyForFlow AND NOT IsErrorMessageVisible;
                usercontrol(FlowAddin;"Microsoft.Dynamics.Nav.Client.FlowIntegration")
                {
                    ApplicationArea = Basic,Suite;
    
                    trigger ControlAddInReady()
                    begin
                    /*     CurrPage.FlowAddin.Initialize(
                            FlowServiceManagement.GetFlowUrl,FlowServiceManagement.GetLocale,
                            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetFlowARMResourceUrl,FlowServiceManagement.GetFlowResourceName,false),
                            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetAzureADGraphhResourceUrl,FlowServiceManagement.GetFlowResourceName,false),
                            AzureAdMgt.GetAccessToken(FlowServiceManagement.GetMicrosoftGraphhResourceUrl,FlowServiceManagement.GetFlowResourceName,false));
    
                        LoadFlows;
    
                        AddInReady := true; */
                    end;
        ```  

    
    3. PowerBIServiceMgt.Codeunit.al (ImportReportRequest)


<!--
The AL compiler is more strict than the C/SIDE compiler and will issue errors for constructs that are not valid. We recommend that you fix the errors in the C/AL solution and re-export, iterating over steps 1-4 above until all the compilation errors are fixed.-->

<!--
1. Fixing errors in AzureADUserManagement.Codeunit.al, FlowSelectorTemplate.Page.al, and FlowSelector.Page.al.

    To fix these errors, comment out the code that errors, as shown: 

        - AzureADUserManagement.Codeunit.al
    
            ```
                local procedure GetGraphUserPlans(var TempPlan: Record Plan temporary;var GraphUser: DotNet UserInfo;IncludePlansWithoutEntitlement: Boolean)
            var
                AssignedPlan: DotNet ServicePlanInfo;
                DirectoryRole: DotNet RoleInfo;
                ServicePlanIdValue: Variant;
                IsSystemRole: Boolean;
                HaveAssignedPlans: Boolean;
            begin
                TempPlan.Reset;
                TempPlan.DeleteAll;
        
                // Loop through assigned Microsoft Entra ID Plans
                foreach AssignedPlan in GraphUser.AssignedPlans do begin
                  HaveAssignedPlans := true;
                  if AssignedPlan.CapabilityStatus = 'Enabled' then begin
                    ServicePlanIdValue := AssignedPlan.ServicePlanId;
                    if IncludePlansWithoutEntitlement or IsNavServicePlan(ServicePlanIdValue) then
                      AddToTempPlan(ServicePlanIdValue,AssignedPlan.ServicePlanName,TempPlan);
                  end;
                end;
        
                // If there are no Microsoft Entra ID Plans, loop through Microsoft Entra roles
                /* if not HaveAssignedPlans then
                  foreach DirectoryRole in Graph.GetUserRoles(GraphUser) do begin
                    Evaluate(IsSystemRole,Format(DirectoryRole.IsSystem));
                    if IncludePlansWithoutEntitlement or IsSystemRole then
                      AddToTempPlan(DirectoryRole.RoleTemplateId,DirectoryRole.DisplayName,TempPlan);
                  end; */
            end;
    
            ```
-->

 
<!--
   2. CodeViewer related errors.

      CodeViewer is no longer used. Either remove all references to it in the application (recommended) or copy the **CodeViewer** folder from the Add-in folder of Business Central 140 RoleTailored client installation to the Add-ins folder of the Business Central 150 Server installation.
-->
<!--
3. Publish the custom application:

    1. Publish the platform system symbols:

        ```
        Publish-NAVApp -ServerInstance BC150 -Path "C:\Program Files (x86)\Microsoft Dynamics 365 Business Central\150\AL Development Environment\System.app" -PackageType SymbolsOnly
        ```
    2. Publish the custom base app extension:

        ```
        Publish-NAVApp -ServerInstance BC150 -Path "C:\Users\jswymer\Documents\AL\CusomtBaseApp2\Microsoft_BaseApp_15.0.34982.0.app" -SkipVerification
        ```
-->
<!--

## Task 11: Create an extensions for your test libary
If solution will use Microsoft (1st party) extensions, you'll have to convert the test toolkit libraries and test runner code units to AL because these extensions have a dependency on the test toolkit. The process is similar to what you did to convert your custom base application to AL.

1. Create a project the same way as you did for the base application, Task 8, steps 1-5.
2. Modify the app.json to include a dependency on your custom  base application extension 

    ```
      "dependencies": [
        {
          "appId": "437dbf0e-84ff-417a-965d-ed2bb9650972",
          "publisher": "Microsoft",
          "name": "Base Application",
          "version": "15.0.0.0"
        }
      ]
    ```
3. Copy all of the AL files for the test library that your generated in Task 5 to the root folder of your test project.

4. Open the **dotnet.al** file for the project, and make the following changes:

    - Delete all instances of `Version = '14.0.0.0';` for **Microsoft.Dynamics.Nav** assembly declarations.
5. Build the project.

    There is a known issue with the XmlTextReader reference in LibraryVerifyXMLSchema.Codeunit.al
 

-->

When all errors are fixed, the custom base application package (.app) will be created.

## Task 8: Create and build an AL project for the test library

If you converted the test library form C/AL to AL, you'll now create and build a project for test library, similar to what you did for the base application.

1. Follow steps 1 through 5 in **Task 7** to create an AL project for the test library.  

2. As with base application project, you have to modify the `app.json` file, but in this case, you have to change the version and add a dependency on the base application that you created.

    |Parameter|Value|
    |---------|-----|
    |`"id":`|Use the default that was created for you.|
    |`"name":`|Specify any valid name.|
    |`"publisher":`|Specify any valid publisher name.|
    |`"version":`|You can use any valid version number, but it's recommended to set it to the same version as the C/AL application.|
    |`"dependencies":`|Set this include information that matches the custom the base application you created in the previous task.|
    |`"platform":`|Set to match the platform version that you're upgrading to: <ul><li>`"20.0.0.0"`- version 20</li><li>`"19.0.0.0"`- version 19</li><li>`"18.0.0.0"`- version 18</li></ul>|
    |`"application":`|Remove this parameter.|
    |`"idRange":`|Set to include all the IDs used by your test application, or leave it blank.|
    |`"runtime":` |Set to match the version that you're upgrading to:<ul><li>`"9.0"`- version 20</li><li>`"8.0"`- version 19</li><li>`"7.0"`- version 18</li></ul>|
    |`"target":"`|Add this parameter and set to `"OnPrem"`.|
    
    For example:

    ```json
    {
        "id": "44445555-eeee-6666-ffff-7777aaaa8888",
        "name": "Test Library",
        "publisher": "Default Publisher",
        "version": "14.13.0.0",
        "brief": "",
        "description": "",
        "privacyStatement": "",
        "EULA": "",
        "help": "",
        "url": "",
        "logo": "",
        "dependencies": [
        {
            "appId": "437dbf0e-84ff-417a-965d-ed2bb9650972",
            "publisher": "Microsoft",
            "name": "Base Application",
            "version": "14.5.0.0"
        }
       ],
        "screenshots": [],
        "platform": "19.0.0.0",
        "idRanges": [  ],
        "contextSensitiveHelpUrl": "https://BC14cal-to-18al-app.com/help/",
        "showMyCode": true,
        "runtime": "8.0",
        "target":  "OnPrem"
    }  
    ```
3. Copy all of the AL files that you generated for the test library in **Task 5** to the root folder of your project.
4. Open the **dotnet.al** file for the project, and make the following changes:

    - Delete all instances of `Version = '14.0.0.0';` for **Microsoft.Dynamics.Nav** assembly declarations.
5. Build the project.

## Next Steps

If you're performing a technical upgrade from version 14.0, return to the technical upgrade step where you left off.
- [Technical Upgrade to to version 20.0](upgrade-technical-upgrade-v14-v20.md#Preparedb)
- [Technical Upgrade to to version 19.0](upgrade-technical-upgrade-v14-v19.md#Preparedb)
- [Technical Upgrade to to version 18.0](upgrade-technical-upgrade-v14-v18.md#Preparedb)



<!--
## Task 11: Publish your project
When your converted solutions compile to an app, you can deploy and run it. You can either create and publish to your own database, or use the recently release Docker AL Preview image. 

### Publishing the AL solution to an empty database 

1. Use the [Create-NAVDatabase](/powershell/module/microsoft.dynamics.nav.ide/create-navdatabase?view=businesscentral-ps) cmdlet to create a new database.

2. Create a new service instance and connect it to the newly created database. 

3. Use the [Publish-NAVApp](/powershell/module/Microsoft.Dynamics.Nav.Apps.Management/Publish-NAVApp?view=businesscentral-ps) cmdlet to publish the System.app by specifying `-PackageType SymbolsOnly`.

4. Add any custom .NET assemblies to your service's Add-Ins folder or create symbolic links in the Add-Ins folder pointing to the folders in your project's `assemblyProbingPaths`. 

5. Publish your extension, either using:  

- PowerShell cmdlets as described in [Publishing and Installing an Extension](../developer/devenv-how-publish-and-install-an-extension-v2.md), or  

- Publish from within Visual Studio Code by defining the server configuration in launch.json and publish with <kbd>Ctrl</kbd>+<kbd>F5</kbd> . 

### Publishing to a Docker AL Preview image
We recently released a Docker AL Preview image as part of the Insider program, containing the BaseApp published as an extension. You can use this to deploy your own modified AL application.

1. Uninstall and unpublish the BaseApp extension and any other extensions that might depend on it. 

2. Add any custom .NET assemblies to your service's Add-Ins folder or create symbolic links in the Add-Ins folder pointing to the folders in your project's `assemblyProbingPaths`. 

3. Publish your extension: 

    - Using the PowerShell cmdlets as described in [Publishing and Installing an Extension](../developer/devenv-how-publish-and-install-an-extension-v2.md). 

    - Publish from within Visual Studio Code by defining the server configuration in `launch.json` and publish with <kbd>Ctrl</kbd>+<kbd>F5</kbd> . 
-->
## Related information
[The Txt2Al Conversion Tool](../developer/devenv-txt2al-tool.md)  
[Developing Extensions](../developer/devenv-dev-overview.md)  
[AL Development Environment](../developer/devenv-reference-overview.md)  
[Page Extension Object](../developer/devenv-page-ext-object.md)  
[Report Object](../developer/devenv-report-object.md)  
[Page Properties](../developer/properties/devenv-page-property-overview.md)
