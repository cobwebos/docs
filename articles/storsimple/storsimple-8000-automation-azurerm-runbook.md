---
title: "使用 Azure 自动化 Runbook 管理 StorSimple 设备 | Microsoft Docs"
description: "了解如何使用 Azure 自动化 Runbook 自动执行 StorSimple 作业"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>使用 Azure 自动化 Runbook 管理 StorSimple 设备

本文介绍如何在 Azure 门户中使用 Azure 自动化 Runbook 管理 StorSimple 8000 系列设备。 此外还提供了一个示例 Runbook，逐步演示如何将环境配置为执行此 Runbook。


## <a name="configure-add-and-run-azure-runbook"></a>配置、添加和运行 Azure Runbook

本部分采用一个适用于 StorSimple 的示例 Windows PowerShell 脚本，详细演示将脚本导入 Runbook，然后发布并执行该 Runbook 所要执行的各个步骤。

### <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：

* 与注册到 StorSimple 8000 系列设备的 StorSimple 设备管理器服务相关联的有效 Azure 订阅。


* 已在计算机（或 StorSimple 的 Windows Server 主机，如果使用）上安装 Windows PowerShell 5.0。


### <a name="create-automation-runbook-module-in-windows-powershell"></a>在 Windows PowerShell 中创建自动化 Runbook 模块

若要创建用于管理 StorSimple 8000 系列设备的自动化模块，请执行以下步骤：

1. 启动 Windows PowerShell。 新建一个文件夹，并将目录更改为新文件夹。

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [将 NuGet CLI 下载](http://www.nuget.org/downloads)到上一步中创建的文件夹下。 nuget.exe 的版本有很多种。 选择与 SDK 对应的版本。 每个下载链接都直接指向 .exe 文件。 请务必右键单击文件，并将它保存到计算机，而不是在浏览器中运行它。

    也可以运行下面的命令，将此脚本下载并存储到先前创建的同一个文件夹中。
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. 下载依赖 SDK。

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. 从示例 GitHub 项目中下载此脚本。

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. 创建用于管理 StorSimple 8000 系列设备的 Azure 自动化 Runbook 模块。 在 Windows PowerShell 窗口中键入以下命令：

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. 验证是否已在 `C:\scripts\StorSimpleSDKTools` 中创建自动化模块 zip 文件。

    ![verify-automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. 通过 Windows PowerShell 创建自动化模块后，会显示以下输出。 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>导入、发布和运行自动化 Runbook

1. 在 Azure 门户中创建 Azure 运行方式自动化帐户。 为此，请转到“Azure Marketplace”>“所有”，并搜索“自动化”。 选择“自动化帐户”。

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. 在“添加自动化帐户”边栏选项卡中：

    1. 提供自动化帐户的**名称**。
    2. 选择已链接到 StorSimple 设备管理器服务的**订阅**。
    3. 创建新的资源组，或选择现有的资源组。
    4. 选择一个**位置**（请尽量选择运行服务的位置）。
    5. 保留选择默认的“创建运行方式帐户”选项。
    5. （可选）选中“固定到仪表板”。 单击“创建” 。

        ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    成功创建自动化帐户后，会收到通知。 有关如何创建自动化帐户的详细信息，请转到[创建运行方式帐户](https://docs.microsoft.com/azure/automation/automation-create-runas-account)。

3. 为了确保创建的自动化帐户可以访问 StorSimple 设备管理器服务，需要将适当的权限分配给自动化帐户。 在 StorSimple 设备管理器服务中转到“访问控制”。 单击“+ 添加”并提供 Azure 自动化帐户的名称。 保存设置。

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. 在新建的帐户中，转到“共享资源”>“模块”并单击“+ 添加模块”。

5. 在“添加模块”边栏选项卡中，浏览到压缩的模块所在的位置，选择并打开该模块。 单击 **“确定”**。

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. 转到“过程自动化”>“Runbook”并单击“+ 添加 Runbook”。 在“添加 Runbook”边栏选项卡中，单击“导入现有的 Runbook”。 指向 **Runbook 文件**的 Windows PowerShell 脚本文件。 系统会自动选择 Runbook 类型。 提供 Runbook 的名称，并选择性地提供说明。 单击“创建” 。

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook 随即会添加到 Runbook 列表。 选择并单击此 Runbook。

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. 编辑 Runbook 并单击“测试”窗格。 提供 StorSimple 设备管理器服务的名称、StorSimple 设备的名称和订阅等参数。 **开始**测试。 完成运行后，会生成报告。 有关详细信息，请转到[如何测试 Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)。

    ![test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. 在测试窗格中检查 Runbook 的输出。 如果对结果满意，请关闭窗格。 单击“发布”，出现确认提示时，请确认并发布该 Runbook。

    ![publish-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>后续步骤

[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。