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
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808037"
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

1. [安装适用于 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md)。

  ```PowerShell  
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

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. 如果使用 **Active Directory 联合身份验证服务**，请使用以下 cmdlet：

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. 以操作员身份登录到 Azure Stack。 有关说明，请参阅[以操作员身份登录到 Azure Stack](../azure-stack-powershell-configure-admin.md)。

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. 将 Windows Server 2016 映像添加到 Azure Stack Marketplace。

    **添加 AzsPlatformimage** cmdlet 用于将映像添加指定的 Azure 资源管理器模板用于引用的 VM 映像的值。
    
    这些值包括：
    
  - **publisher**  
    例如： `Microsoft`  
    VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 此字段不得包含空格或其他特殊字符。  
  - **offer**  
    例如： `WindowsServer`  
    VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 此字段不得包含空格或其他特殊字符。  
  - **sku**  
    例如： `Datacenter2016`  
    VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 此字段不得包含空格或其他特殊字符。  
  - **version**  
    例如： `1.0.0`  
    VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 例如，**1.0.0**。 此字段不得包含空格或其他特殊字符。  
  - **osType**  
    例如： `Windows`  
    映像的 osType 必须为 **Windows** 或 **Linux**。 替换*fully_qualified_path_to_ISO*替换为你下载 Windows Server 2016 ISO 的路径。 
  - **OSUri**  
    例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    可以指定 `osDisk` 的 Blob 存储 URI。 在本例中，你将指定存储下载的映像的位置。

    有关详细信息，请参阅的 PowerShell 参考[添加 AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet。

    使用权限提升的提示符打开 PowerShell，并运行：

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

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
