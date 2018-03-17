---
title: "从本地源添加 Azure 堆栈应用商店项 |Microsoft 文档"
description: "描述如何将本地操作系统映像添加到 Azure 堆栈应用商店。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 296719ddd23fb9ee717455420906e9a634a71a8d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>教程： 从本地源添加 Azure 堆栈应用商店项

作为 Azure 堆栈操作员，你需要要做，以使用户能够部署虚拟机 (VM) 的第一个操作是将 VM 映像添加到 Azure 堆栈应用商店。 默认情况下执行任何操作将发布到 Azure 堆栈应用商店，但你可以上载 VM ISO 映像，使它们可为你的用户。 如果你已经部署了 Azure 堆栈中断开连接的情况下或在方案中通过有限的连接，请使用此选项。

在本教程中，你下载的 Windows Server 2016 VM 映像从 Windows Server 评估页上，并将其上载到 Azure 堆栈市场。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加 Windows Server 2016 VM 映像
> * 验证 VM 映像可用 
> * 测试的 VM 映像

## <a name="prerequisites"></a>必备组件

完成本教程：

- 安装[Azure 堆栈兼容 Azure PowerShell 模块](asdk-post-deploy.md#install-azure-stack-powershell)
- 下载[Azure 堆栈工具](asdk-post-deploy.md#download-the-azure-stack-tools)
- 下载[Windows 服务器 2106年虚拟机的 ISO 映像](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)从 Windows Server 评估页

## <a name="add-a-windows-server-vm-image"></a>添加 Windows Server VM 映像
通过添加一个以前下载的 ISO 映像，使用 PowerShell，可以发布到 Azure 堆栈市场的 Windows Server 2016 映像。 

如果你已经部署了 Azure 堆栈中断开连接的情况下或在方案中通过有限的连接，请使用此选项。

1. 导入 Azure 堆栈`Connect`和`ComputeAdmin`PowerShell 模块包含在 Azure 堆栈工具目录中通过使用以下命令：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. 运行到具体取决于你是否通过使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 部署你的 Azure 堆栈环境你 ASDK 主机计算机的以下脚本之一：

  - 命令**Azure AD 部署**: 

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

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - 命令**AD FS 部署**:
      
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

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. 将 Windows Server 2016 映像添加到 Azure 堆栈市场。 （将 *fully_qualified_path_to_ISO* 替换为下载的 Windows Server 2016 ISO 的路径。）

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>验证应用商店项目可用
使用以下步骤以验证新的 VM 映像 Azure 堆栈应用商店中可用。

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 选择**更多的服务** > **应用商店管理**。 

3. 验证已成功添加 Windows Server 2016 数据中心 VM 映像。

   ![从 Azure 下载的映像](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>测试的 VM 映像
作为 Azure 堆栈运算符，你可以使用[管理员门户](https://adminportal.local.azurestack.external)来创建测试 VM 以验证映像可成功。 

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。

2. 单击**新** > **计算** > **Windows Server 2016 数据中心** > **创建**。  
 ![从应用商店映像创建 VM](media/asdk-marketplace-item/new-compute.png)

3. 在**基础知识**边栏选项卡，输入以下信息，然后单击**确定**:
  - **名称**： 测试-vm-1
  - **用户名称**: AdminTestUser
  - **密码**: AzS TestVM01
  - **订阅**： 接受默认提供程序订阅
  - **资源组**： 测试 vm rg
  - **位置**： 本地

4. 在“选择大小”边栏选项卡中，单击“A1 标准”，并单击“选择”。  

5. 在“设置”边栏选项卡中接受默认值，然后单击“确定”

6. 在“摘要”边栏选项卡中，单击“确定”创建虚拟机。  
> [!NOTE]
> 虚拟机部署需要几分钟才能完成。

7. 若要查看到新的 VM，请单击**虚拟机**，然后搜索**测试 vm 1**并单击其名称。
    ![显示的第一个测试 VM 映像](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>清理资源
您已成功登录到 VM，并验证测试映像正常工作后，应删除 test 资源组。 这将释放到单节点 ASDK 安装的可用资源有限。

当不再需要时，删除资源组、 VM 和所有相关的资源，通过执行以下步骤：

1. 登录到[ASDK 管理员门户](https://adminportal.local.azurestack.external)。
2. 单击**资源组** > **测试 vm rg** > **删除资源组**。
3. 类型**测试 vm rg**作为资源组名称然后单击**删除**。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加 Windows Server 2016 VM 映像
> * 验证 VM 映像可用 
> * 测试的 VM 映像

前进到下一步的教程，若要了解如何创建 Azure 堆栈优惠和计划。

> [!div class="nextstepaction"]
> [提供 Azure 堆栈 IaaS 服务](asdk-offer-services.md)