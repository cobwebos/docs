---
title: 从本地源添加 Azure Stack Marketplace 项 | Microsoft Docs
description: 介绍如何将本地操作系统映像添加到 Azure Stack Marketplace。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 325ef42d72970f4e0962a9b1a81b78bbd39585d4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>教程：从本地源添加 Azure Stack Marketplace项

若要让用户能够部署虚拟机 (VM)，Azure Stack 操作员要做的第一件事是将 VM 映像添加到 Azure Stack Marketplace。 默认情况下，系统不会将任何项发布到 Azure Stack Marketplace，但你可以上传 VM ISO 映像供用户使用。 如果在离线场景或连接受限的场景中部署 Azure Stack，请使用此选项。

在本教程中，我们将从“Windows Server 评估”页下载 Windows Server 2016 VM 映像，并将其上传到 Azure Stack Marketplace。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Windows Server 2016 VM 映像
> * 验证是否已提供 VM 映像 
> * 测试 VM 映像

## <a name="prerequisites"></a>必备组件

完成本教程：

- 安装 [Azure Stack 兼容的 Azure PowerShell 模块](asdk-post-deploy.md#install-azure-stack-powershell)
- 下载 [Azure Stack 工具](asdk-post-deploy.md#download-the-azure-stack-tools)
- 从“Windows Server 评估”页下载 [Windows Server 2016 虚拟机 ISO 映像](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)

## <a name="add-a-windows-server-vm-image"></a>添加 Windows Server VM 映像
可以使用 PowerShell 添加前面所下载的 ISO 映像，以将 Windows Server 2016 映像发布到 Azure Stack Marketplace。 

如果在离线场景或连接受限的场景中部署 Azure Stack，请使用此选项。

1. 使用以下命令导入 Azure Stack 工具目录中包含的 Azure Stack `Connect` 和 `ComputeAdmin` PowerShell 模块：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. 在 ASDK 主机上，根据部署 Azure Stack 环境时使用的是 Azure Active Directory (Azure AD) 还是 Active Directory 联合身份验证服务 (AD FS) 运行以下脚本之一：

  - 适用于 **Azure AD 部署**的命令： 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - 适用于 **AD FS 部署**的命令：
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

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

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. 将 Windows Server 2016 映像添加到 Azure Stack Marketplace。 （将 *fully_qualified_path_to_ISO* 替换为下载的 Windows Server 2016 ISO 的路径。）

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>验证是否已提供 Marketplace 项
使用以下步骤，验证新的 VM 映像是否已在 Azure Stack Marketplace 中提供。

1. 登录到 [ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “Marketplace 管理”。 

3. 验证是否已成功添加 Windows Server 2016 Datacenter VM 映像。

   ![从 Azure 下载的映像](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>测试 VM 映像
Azure Stack 操作员可以使用[管理员门户](https://adminportal.local.azurestack.external)来创建测试 VM，以验证是否已成功提供映像。 

1. 登录到 [ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 单击“新建” > “计算” > “Windows Server 2016 Datacenter” > “创建”。  
 ![从 Marketplace 映像创建 VM](media/asdk-marketplace-item/new-compute.png)

3. 在“基本信息”边栏选项卡中输入以下信息，并单击“确定”：
  - **名称**：test-vm-1
  - **用户名**：AdminTestUser
  - **密码**：AzS TestVM01
  - **订阅**：接受“默认提供商订阅”
  - **资源组**：test-vm-rg
  - **位置**：本地

4. 在“选择大小”边栏选项卡中，单击“A1 标准”，并单击“选择”。  

5. 在“设置”边栏选项卡中接受默认值，然后单击“确定”

6. 在“摘要”边栏选项卡中，单击“确定”创建虚拟机。  
> [!NOTE]
> 虚拟机部署需要几分钟时间才能完成。

7. 若要查看新 VM，请单击“虚拟机”，然后搜索 **test-vm-1** 并单击其名称。
    ![显示的第一个测试 VM 映像](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>清理资源
成功登录到 VM 并验证测试映像可正常运行后，应删除测试资源组。 这会释放有限的资源供单节点 ASDK 安装使用。

如果不再需要资源组、VM 和所有相关资源，请遵循以下步骤将其删除：

1. 登录到 [ASDK 管理员门户](https://adminportal.local.azurestack.external)。
2. 单击“资源组” > “test-vm-rg” > “删除资源组”。
3. 键入 **test-vm-rg** 作为资源组名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Windows Server 2016 VM 映像
> * 验证是否已提供 VM 映像 
> * 测试 VM 映像

继续学习下一篇教程，了解如何创建 Azure Stack 产品/服务和计划。

> [!div class="nextstepaction"]
> [提供 Azure Stack IaaS 服务](asdk-offer-services.md)
