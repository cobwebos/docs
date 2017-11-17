---
title: "将 VM 映像添加到 Azure 堆栈 |Microsoft 文档"
description: "添加供租户用你的组织的自定义 Windows 或 Linux VM 映像。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 54b6a6984e66f32642336f4ea5e1e9f4ec9d03f3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>在 Azure 堆栈中提供的自定义虚拟机映像

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈中运算符可以提供自定义虚拟机映像为其用户。 可以通过 Azure 资源管理器模板引用这些映像，或可以将它们添加到 Azure Marketplace UI 为应用商店项。 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>通过使用 PowerShell 将 VM 映像添加到应用商店

运行以下先决条件，从[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或从基于 Windows 的外部客户端，如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [安装适用于 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。  

2. 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。  

3. 准备 VHD 格式中的 Windows 或 Linux 的操作系统虚拟硬盘映像 （请勿使用 VHDX 格式）。
   
   * 有关 Windows 映像，有关准备的映像的说明，请参阅[将 Windows VM 映像上载到 Azure 资源管理器部署](../virtual-machines/windows/upload-generalized-managed.md)。
   * 对于 Linux 映像，请参阅[Azure 堆栈上部署 Linux 虚拟机](azure-stack-linux.md)。 完成准备的映像，或使用现有 Azure 堆栈 Linux 映像，如文章中所述的步骤。  

若要将映像添加到 Azure 堆栈应用商店，请完成以下步骤：

1. 导入连接和 ComputeAdmin 模块：
   
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
          -EnvironmentName AzureStackAdmin 

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID 
        ```
    
3. 添加 VM 映像，通过调用`Add-AzsVMImage`cmdlet。 在`Add-AzsVMImage`cmdlet，指定`osType`作为 Windows 或 Linux。 包括发布者、 产品/服务、 SKU、 和的 VM 映像的版本。 允许参数信息，请参阅[参数](#parameters)。 通过 Azure 资源管理器模板使用的参数用于引用的 VM 映像。 下面的示例调用该脚本：
     
  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

该命令执行以下任务：

* 向 Azure 堆栈环境进行身份验证。
* 将本地 VHD 上载到新创建的临时存储帐户。
* 将 VM 映像添加到 VM 映像存储库。
* 创建应用商店项。

若要验证成功，该命令运行在门户中，请转到应用商店。 验证 VM 映像可用于**虚拟机**类别。

![已成功添加的 VM 映像](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>使用 PowerShell 删除 VM 映像

当您不再需要你上载的虚拟机映像时，可以从应用商店使用以下 cmdlet 来删除它：

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>parameters

| 参数 | 说明 |
| --- | --- |
| **发布服务器** |用户使用时它们将映像部署的 VM 映像发布者名称段。 一个示例是**Microsoft**。 不包括空格或其他特殊字符在此字段中。 |
| **产品/服务** |用户使用用户部署的 VM 映像时的 VM 映像提供名称段。 一个示例是**WindowsServer**。 不包括空格或其他特殊字符在此字段中。 |
| **sku** |用户使用用户部署的 VM 映像时的 VM 映像的 SKU 名称段。 一个示例是**Datacenter2016**。 不包括空格或其他特殊字符在此字段中。 |
| **version** |用户使用用户部署的 VM 映像时的 VM 映像版本。 此版本采用以下格式 *\#。\#。\#* 一个示例是**1.0.0**。 不包括空格或其他特殊字符在此字段中。 |
| **osType** |图像的 osType 必须是**Windows**或**Linux**。 |
| **osDiskLocalPath** |OS 磁盘 VHD 上载到 Azure 堆栈的 VM 映像为本地路径。 |
| **dataDiskLocalPaths** |本地路径可以作为 VM 映像的一部分上载的数据磁盘的可选数组。 |
| **CreateGalleryItem** |一个布尔型标志，用于确定是否在应用商店中创建项目。 默认情况下，它设置为**true**。 |
| **标题** |应用商店项的显示名称。 默认情况下，它设置为`Publisher-Offer-Sku`的 VM 映像的值。 |
| **说明** |应用商店项目的描述。 |
| **位置** |应该发布的 VM 映像的位置。 默认情况下，此值设置为**本地**。|
| **osDiskBlobURI** |（可选）此脚本还接受 Blob 存储 URI 为`osDisk`。 |
| **dataDiskBlobURIs** |（可选）此脚本还接受用于将数据磁盘添加到映像的 Blob 存储 Uri 的数组。 |

## <a name="add-a-vm-image-through-the-portal"></a>添加通过门户的 VM 映像

> [!NOTE]
> 使用此方法，你必须单独创建的应用商店项。

映像必须能够将引用的 Blob 存储 URI。 准备 VHD 格式 (不 VHDX) 中的 Windows 或 Linux 操作系统映像，然后将映像上载到 Azure 或 Azure 堆栈中的存储帐户。 如果你的映像已上载到 Blob 存储在 Azure 中或 Azure 堆栈，则可以跳过步骤 1。

1. [将 Windows VM 映像上载到 Azure 资源管理器部署](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)或 Linux 映像，请按照中所述的说明进行操作[Azure 堆栈上部署 Linux 虚拟机](azure-stack-linux.md)。 在上载映像之前，务必要考虑以下因素：

   * 它会更加高效，若要将图像上载到 Azure 堆栈 Blob 存储与到 Azure Blob 存储，因为它采用更快速地将图像推送到 Azure 堆栈映像存储库。 
   
   * 当你上载[Windows VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)，请确保**登录到 Azure**与单步[配置 Azure 堆栈运算符 PowerShell 环境](azure-stack-powershell-configure-admin.md)步骤。  

   * 记下你将上载映像的位置的 URI 的 Blob 存储。 Blob 存储 URI 具有以下格式：  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd。

   * 若要使 blob 进行匿名访问，请转到存储帐户 blob 容器 VM 映像 VHD 已在其中上载。 选择**Blob**，然后选择**访问策略**。 （可选） 你可以改为生成共享的访问签名的容器，并将其作为 blob URI 的一部分包含。

   ![转到存储帐户 blob](./media/azure-stack-add-vm-image/image1.png)

   ![设置到公共 blob 访问](./media/azure-stack-add-vm-image/image2.png)

2. 运算符以登录到 Azure 堆栈。 在菜单中，选择**更多的服务** > **资源提供程序**。 然后，选择**计算** > **VM 映像** > **添加**。

3. 下**添加 VM 映像**，输入发布者、 产品/服务、 SKU、 和版本的虚拟机映像。 这些名称段指资源管理器模板中的 VM 映像。 请务必选择**osType**正确值。 有关**OD 磁盘 Blob URI**，输入已上载的图像的 Blob URI。 然后，选择**创建**若要开始创建 VM 映像。
   
   ![开始创建映像](./media/azure-stack-add-vm-image/image4.png)

   已成功创建映像时，VM 映像状态将更改为**Succeeded**。

4. 为了让虚拟机映像在 UI 中供用户使用更易于使用，它是一个好主意[创建应用商店项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>后续步骤

[预配虚拟机](azure-stack-provision-vm.md)