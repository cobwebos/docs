---
title: 在 Azure Stack Edge 设备上设置 Azure 资源管理器密码
description: 介绍如何使用 Azure PowerShell 连接到 Azure Stack Edge 上运行的 Azure 资源管理器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2020
ms.author: alkohli
ms.openlocfilehash: d56f12c746383576a32e2c0ade542bc8aedff22d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083774"
---
# <a name="set-azure-resource-manager-password"></a>设置 Azure 资源管理器密码

[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]

本文介绍如何设置 Azure 资源管理器密码。 在通过 Azure 资源管理器连接到设备本地 Api 时，需要设置此密码。

根据你使用的是 Azure 门户还是 PowerShell cmdlet，设置密码的过程可能会有所不同。 以下各部分介绍了上述每个过程。


## <a name="reset-password-via-the-azure-portal"></a>通过 Azure 门户重置密码

1. 在 Azure 门户中，请切换到你创建的 Azure Stack Edge 资源来管理你的设备。 转到“Edge 计算”>“开始”。

2. 在右侧窗格中，从命令栏中选择 " **重置边缘 ARM 密码**"。 

    ![重置 EdgeARM 用户密码1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. 在 " **重置 EdgeArm 用户密码** " 边栏选项卡中，提供密码以通过 Azure 资源管理器连接到设备本地 api。 确认密码，然后选择 " **重置**"。

    ![重置 EdgeARM 用户密码2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>通过 PowerShell 重置密码

1. 在 Azure 门户中，切换到你创建的 Azure Stack Edge 资源来管理你的设备。 请记下 " **概述** " 页中的以下参数。

    - Azure Stack Edge 资源名称
    - 订阅 ID

2. 请 **> 属性**中转到 "设置"。 请记下 " **属性** " 页中的以下参数。

    - 资源组
    - CIK 加密密钥：选择 "查看"，然后复制 **加密密钥**。

    ![获取 CIK 加密密钥](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. 标识将用于连接到 Azure 资源管理器的密码。

4. 启动 cloud shell。 在右上角的图标上选择：

    ![启动 cloud shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    启动 cloud shell 后，你可能需要切换到 PowerShell。

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. 设置上下文。 键入：

    `Set-AzContext -SubscriptionId <Subscription ID>`

    下面是示例输出：

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  如果有旧的 PS 模块，则需要安装这些模块。

    `Remove-Module  Az.DataBoxEdge -force`

    下面是一个示例输出。 在此示例中，没有要安装的旧模块。

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. 下一组命令将下载并运行脚本以安装 PowerShell 模块。
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. 在接下来的一组命令中，你将需要提供你在上一步中标识的资源名称、资源组名称、加密密钥和密码。

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    密码和加密密钥参数必须作为安全字符串传递。 使用以下 cmdlet 将密码和加密密钥转换为安全字符串。

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    在 AzDataBoxEdgeUser cmdlet 中使用上述生成的安全字符串作为参数来重置密码。 使用创建 Azure Stack Edge/Data Box Gateway 资源时使用的同一资源组。

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    下面是示例输出。
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
使用新密码连接到 Azure 资源管理器。

## <a name="next-steps"></a>后续步骤

[连接到 Azure 资源管理器](azure-stack-edge-j-series-connect-resource-manager.md)