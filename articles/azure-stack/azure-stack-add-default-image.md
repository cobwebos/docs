---
title: "将默认 VM 映像添加到 Azure 堆栈 Marketplace |Microsoft 文档"
description: "将 Windows Server 2016 VM 默认映像添加到 Azure 堆栈应用商店。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e5e7ab55800eccea98cbd71ae32bdc611ee3e961
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>将 Windows Server 2016 VM 映像添加到 Azure 堆栈应用商店

默认情况下，任何虚拟机映像中提供不了 Azure 堆栈应用商店。 Azure 堆栈运算符必须将映像添加到用户访问应用商店。 通过使用以下方法之一，可以向 Azure 堆栈应用商店添加 Windows Server 2016 映像：

* [从 Azure 应用商店下载映像](#add-the-image-by-downloading-it-from-the-azure-marketplace)。 如果你正在连接方案中工作并且你向 Azure 注册你的 Azure 堆栈实例，请使用此选项。

* [将映像添加使用 PowerShell](#add-the-image-by-using-powershell)。 如果你已经部署了 Azure 堆栈中断开连接的情况下，或在方案中通过有限的连接，请使用此选项。

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>将映像添加通过从 Azure 应用商店中下载它

1. 部署 Azure 堆栈，然后登录到你的 Azure 堆栈开发工具包。

2. 选择**更多的服务** > **应用商店管理** > **从 Azure 中的添加**。 

3. 查找或搜索**Windows Server 2016 数据中心 – Eval**映像，，然后选择**下载**。

   ![从 Azure 下载图像](media/azure-stack-add-default-image/download-image.png)

下载完成后，该映像位于下**应用商店管理**。 映像也是可用在**虚拟机**。

## <a name="add-the-image-by-using-powershell"></a>使用 PowerShell 添加映像

### <a name="prerequisites"></a>必备组件 

运行以下先决条件，从[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或从基于 Windows 的外部客户端，如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  

2. 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。  

3. 在 Windows Server 评估页上，[下载 Windows Server 2016 评估版](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)。 出现提示时，选择下载的 ISO 版本。 记录到更高版本在本文中所述的步骤中使用的下载位置的路径。 此步骤需要 internet 连接。  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>将映像添加到 Azure 堆栈应用商店
   
1. 通过使用以下命令导入 Azure 堆栈连接和 ComputeAdmin 模块：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. 登录到你的 Azure 堆栈环境。 运行以下脚本，具体取决于你是否通过使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 部署你的 Azure 堆栈环境之一。 (替换 Azure AD `tenantName`，`GraphAudience`终结点，和`ArmEndpoint`值以反映你环境的配置。)  

   * **Azure Active Directory**。 使用以下 cmdlet:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Active Directory 联合身份验证服务**。 使用以下 cmdlet:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. 将 Windows Server 2016 映像添加到 Azure 堆栈应用商店。 (替换*fully_qualified_path_to_ISO*替换为你下载 Windows Server 2016 ISO 的路径。)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

若要确保 Windows Server 2016 VM 映像具有最新的累积更新，包括`IncludeLatestCU`参数在运行时`New-AzsServer2016VMImage`cmdlet。 有关允许的参数信息`New-AzsServer2016VMImage`cmdlet，请参阅[参数](#parameters)。 它采用大约一小时才能将该映像发布到 Azure 堆栈应用商店。 

## <a name="parameters"></a>parameters

|新 AzsServer2016VMImage 参数|必需？|说明|
|-----|-----|------|
|ISOPath|是|下载 Windows Server 2016 ISO 完全限定的路径。|
|net35|否|在 Windows Server 2016 映像上安装.NET 3.5 运行时。 默认情况下，此值设置为**true**。|
|版本|否|指定**核心**，**完整**，或**同时**Windows Server 2016 映像。 默认情况下，此值设置为**完整**。|
|VHDSizeInMB|否|设置要添加到你的 Azure 堆栈环境的 VHD 映像的大小 （以 mb 为单位）。 默认情况下，此值设置为 40,960 MB。|
|CreateGalleryItem|否|指定是否应为 Windows Server 2016 映像创建的应用商店项。 默认情况下，此值设置为**true**。|
|location |否 |指定的 Windows Server 2016 映像应将其发布到的位置。|
|IncludeLatestCU|否|适用于新的 VHD 的最新的 Windows Server 2016 累积更新。|
|CUUri |否 |设置 Windows Server 2016 累积更新为特定 URI 从运行中。 |
|CUPath |否 |设置 Windows Server 2016 累积更新从本地路径中运行。 此选项很有帮助，如果你已经部署了连接断开的环境中的 Azure 堆栈实例。|

## <a name="next-steps"></a>后续步骤

[预配虚拟机](azure-stack-provision-vm.md)
