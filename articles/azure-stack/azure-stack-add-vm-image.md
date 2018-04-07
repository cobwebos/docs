---
title: 向 Azure Stack 添加 VM 映像 | Microsoft Docs
description: 添加组织的自定义 Windows 或 Linux VM 映像，供租户使用。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: mabrigg
ms.openlocfilehash: eb2035f6e667a9b3ab642d42cb9bb5ecf5c86fb1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>在 Azure Stack 中提供自定义虚拟机映像

适用于：Azure Stack 集成系统和 Azure Stack 开发工具包

在 Azure Stack 中，操作员可以将自定义虚拟机映像提供给用户。 可以让 Azure 资源管理器模板引用这些映像，也可以将它们作为 Marketplace 项添加到 Azure Marketplace UI。

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>使用 PowerShell 将 VM 映像添加到 Marketplace

如果已[通过 VPN 进行连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)，请通过[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或基于 Windows 的外部客户端运行以下先决条件：

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

3. 准备 VHD 格式（请勿使用 VHDX 格式）的 Windows 或 Linux 操作系统虚拟硬盘映像。

   * 对于 Windows 映像，若要获取如何准备映像的说明，请参阅[将 Windows VM 映像上传到 Azure 以进行资源管理器部署](../virtual-machines/windows/upload-generalized-managed.md)。

   * 对于 Linux 映像，请参阅[在 Azure Stack 上部署 Linux 虚拟机](azure-stack-linux.md)。 根据文章中的说明，完成准备映像的步骤，或者使用现有的 Azure Stack Linux 映像。    

   Azure Stack 支持固定的磁盘 VHD 格式。 固定格式在文件内对逻辑磁盘采用线性结构，使磁盘偏移量 X 存储在 Blob 偏移量 X 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 若要确认磁盘是否为固定格式，请使用 [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 命令。  

   > [!IMPORTANT]
   >  Azure Stack 不支持动态磁盘 VHD。 重设附加到 VM 的动态磁盘的大小会导致 VM 处于故障状态。 若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，接着重新创建虚拟机。

若要将映像添加到 Azure Stack Marketplace，请完成以下步骤：

1. 导入 Connect 和 ComputeAdmin 模块：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ```

2. 登录到 Azure Stack 环境。 运行以下脚本之一，具体取决于部署 Azure Stack 环境时是使用了 Azure Active Directory (Azure AD) 还是 Active Directory 联合身份验证服务 (AD FS)。 （根据环境配置替换 Azure AD `tenantName`、`GraphAudience` 终结点和 `ArmEndpoint` 值。）

    * **Azure Active Directory**。 使用以下 cmdlet：

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

   * **Active Directory 联合身份验证服务**。 使用以下 cmdlet：

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

3. 通过调用 `Add-AzsVMImage` cmdlet 添加 VM 映像。 在 `Add-AzsVMImage` cmdlet 中，将 `osType` 指定为 Windows 或 Linux。 包括 VM 映像的发布者、产品/服务、SKU 和版本。 若要了解允许的参数，请参阅[参数](#parameters)。 这些参数由 Azure 资源管理器模板用来引用 VM 映像。 以下示例调用此脚本：

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```


命令执行以下操作：

* 向 Azure Stack 环境进行身份验证。
* 将本地 VHD 上传到新创建的临时存储帐户。
* 将 VM 映像添加到 VM 映像存储库。
* 创建 Marketplace 项。

若要验证命令是否已成功运行，请在门户中转到 Marketplace。 验证 VM 映像可用于**计算**类别。

![VM 映像已成功添加](./media/azure-stack-add-vm-image/verify-vm.png)

## <a name="remove-a-vm-image-by-using-powershell"></a>使用 PowerShell 删除 VM 映像

不再需要上传的虚拟机映像时，可使用以下 cmdlet 从 Marketplace 中删除它：

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
| **publisher** |VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 此字段不得包含空格或其他特殊字符。 |
| **offer** |VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 此字段不得包含空格或其他特殊字符。 |
| **sku** |VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 此字段不得包含空格或其他特殊字符。 |
| **version** |VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 例如，**1.0.0**。 此字段不得包含空格或其他特殊字符。 |
| **osType** |映像的 osType 必须为 **Windows** 或 **Linux**。 |
| **osDiskLocalPath** |OS 磁盘 VHD 的本地路径，该 VHD 将作为 VM 映像上传到 Azure Stack。 |
| **dataDiskLocalPaths** |数据磁盘的本地路径的可选数组，这些磁盘可以作为 VM 映像的一部分上传。 |
| **CreateGalleryItem** |一个布尔标志，用于确定是否在 Marketplace 中创建项。 此项默认设置为 **true**。 |
| **title** |Marketplace 项的显示名称。 此项默认设置为 VM 映像的 `Publisher-Offer-Sku` 值。 |
| **说明** |Marketplace 项的说明。 |
| **位置** |应该发布 VM 映像的位置。 默认情况下，此值设置为 **local**。|
| **osDiskBlobURI** |（可选）此脚本也接受 Blob 存储 URI 作为 `osDisk`。 |
| **dataDiskBlobURIs** |（可选）此脚本也接受通过 Blob 存储 URI 的数组将数据磁盘添加到映像的做法。 |

## <a name="add-a-vm-image-through-the-portal"></a>通过门户添加 VM 映像

> [!NOTE]
> 使用此方法时，必须单独创建 Marketplace 项。

映像必须能够通过 Blob 存储 URI 进行引用。 以 VHD（不是 VHDX）格式准备 Windows 或 Linux 操作系统映像，然后将映像上传到 Azure 或 Azure Stack 中的存储帐户。 如果映像已上传到 Azure 或 Azure Stack 中的 Blob 存储，则可跳过步骤 1。

1. [将 Windows VM 映像上传到 Azure 以进行资源管理器部署](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)；对于 Linux 映像，请按[在 Azure Stack 上部署 Linux 虚拟机](azure-stack-linux.md)中的说明操作。 在上传映像之前，必须考虑以下因素：

   * Azure Stack 支持固定的磁盘 VHD 格式。 固定格式在文件内对逻辑磁盘采用线性结构，使磁盘偏移量 X 存储在 Blob 偏移量 X 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 若要确认磁盘是否为固定格式，请使用 [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 命令。  

    > [!IMPORTANT]
   >  Azure Stack 不支持动态磁盘 VHD。 重设附加到 VM 的动态磁盘的大小会导致 VM 处于故障状态。 若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，接着重新创建虚拟机。

   * 将映像上传到 Azure Stack Blob 存储比上传到 Azure Blob 存储更高效，因为将映像推送到 Azure Stack 映像存储库的时间更短。

   * 上传 [Windows VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)时，请确保将“登录到 Azure”步骤替换为[配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)步骤。  

   * 记下在其中上传映像的 Blob 存储 URI。 Blob 存储 URI 的格式如下：*&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd。

   * 若要使 Blob 可供匿名访问，请转到已在其中上传了 VM 映像 VHD 的存储帐户 Blob 容器。 选择“Blob”，然后选择“访问策略”。 也可改为生成容器的共享访问签名，然后将其作为 Blob URI 的一部分包括进去。

   ![转到存储帐户 Blob](./media/azure-stack-add-vm-image/image1.png)

   ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/image2.png)

2. 以操作员身份登录到 Azure Stack。 在菜单中，选择“更多服务” > “资源提供程序”。 然后，选择“计算” > “VM 映像” > “添加”。

3. 在“添加 VM 映像”下，输入虚拟机映像的发布者、产品/服务、SKU 和版本。 这些名称段是指资源管理器模板中的 VM 映像。 确保正确地选择 **osType** 值。 对于“OS 磁盘 Blob URI”，请输入在其中上传了映像的 Blob URI。 然后选择“创建”，开始创建 VM 映像。

   ![开始创建映像](./media/azure-stack-add-vm-image/image4.png)

   成功创建映像后，VM 映像状态会更改为“已成功”。

4. 为了方便用户在 UI 中使用虚拟机映像，可以[创建 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>后续步骤

[预配虚拟机](azure-stack-provision-vm.md)
