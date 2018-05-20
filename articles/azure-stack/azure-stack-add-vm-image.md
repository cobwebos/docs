---
title: 添加和删除到 Azure 堆栈的 VM 映像 |Microsoft 文档
description: 添加或删除供租户用你的组织的自定义 Windows 或 Linux VM 映像。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 39708248160b029185b64ed927a453562e1003f2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>在 Azure 堆栈中提供的虚拟机映像

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈中你可以向虚拟机映像提供你的用户。 可以让 Azure 资源管理器模板引用这些映像，也可以将它们作为 Marketplace 项添加到 Azure Marketplace UI。 你可以使用以映像形式全局 Azure 应用商店，或添加自定义映像。 你可以添加使用此门户或 Windows PowerShell 的 VM。

## <a name="add-a-vm-image-through-the-portal"></a>通过门户添加 VM 映像

> [!NOTE]
> 使用此方法时，必须单独创建 Marketplace 项。

映像必须能够将引用的 blob 存储 URI。 以 VHD（不是 VHDX）格式准备 Windows 或 Linux 操作系统映像，然后将映像上传到 Azure 或 Azure Stack 中的存储帐户。 如果你的映像已上载到 blob 存储在 Azure 中或 Azure 堆栈，则可以跳过步骤 1。

1. [将 Windows VM 映像上传到 Azure 以进行资源管理器部署](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)；对于 Linux 映像，请按[在 Azure Stack 上部署 Linux 虚拟机](azure-stack-linux.md)中的说明操作。 在上传映像之前，必须考虑以下因素：

   - Azure Stack 支持固定的磁盘 VHD 格式。 固定格式在文件内对逻辑磁盘采用线性结构，使磁盘偏移量 X 存储在 Blob 偏移量 X 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 若要确认磁盘是否为固定格式，请使用 [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 命令。  

    > [!IMPORTANT]
    >  Azure Stack 不支持动态磁盘 VHD。 重设附加到 VM 的动态磁盘的大小会导致 VM 处于故障状态。 若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，接着重新创建虚拟机。

   * 它会将映像上载到 Azure 堆栈 blob 存储不是因为它采用更快速地将图像推送到 Azure 堆栈映像存储库，则到 Azure blob 存储更加高效。

   * 上传 [Windows VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)时，请确保将“登录到 Azure”步骤替换为[配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)步骤。  

   * 记下你将上载映像的位置的 URI 的 blob 存储。 Blob 存储 URI 具有以下格式： *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd。

   * 若要使 Blob 可供匿名访问，请转到已在其中上传了 VM 映像 VHD 的存储帐户 Blob 容器。 选择“Blob”，然后选择“访问策略”。 也可改为生成容器的共享访问签名，然后将其作为 Blob URI 的一部分包括进去。

   ![转到存储帐户 Blob](./media/azure-stack-add-vm-image/image1.png)

   ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/image2.png)

2. 以操作员身份登录到 Azure Stack。 在菜单中，选择“更多服务” > “资源提供程序”。 然后，选择“计算” > “VM 映像” > “添加”。

3. 在“添加 VM 映像”下，输入虚拟机映像的发布者、产品/服务、SKU 和版本。 这些名称段是指资源管理器模板中的 VM 映像。 确保正确地选择 **osType** 值。 对于“OS 磁盘 Blob URI”，请输入在其中上传了映像的 Blob URI。 然后选择“创建”，开始创建 VM 映像。

   ![开始创建映像](./media/azure-stack-add-vm-image/image4.png)

   成功创建映像后，VM 映像状态会更改为“已成功”。

4. 为了方便用户在 UI 中使用虚拟机映像，可以[创建 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="remove-a-vm-image-through-the-portal"></a>删除通过门户的 VM 映像

1. 打开管理门户在[ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)。

2. 选择**应用商店管理**，然后选择你想要删除的 VM。

3. 单击“删除” 。

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>通过使用 PowerShell 将 VM 映像添加到应用商店

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 作为一个操作员登录到 Azure 堆栈。 有关说明，请参阅[登录到 Azure 堆栈作为一个操作员](azure-stack-powershell-configure-admin.md)。

3. 使用提升的提示符下打开 PowerShell 并运行：

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  **添加 AzsPlatformimage** cmdlet 将指定的 Azure 资源管理器模板用于引用的 VM 映像的值。 值包括：
  - **publisher**  
    例如： `Canonical`  
    VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 此字段不得包含空格或其他特殊字符。  
  - **offer**  
    例如： `UbuntuServer`  
    VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 此字段不得包含空格或其他特殊字符。  
  - **sku**  
    例如： `14.04.3-LTS`  
    VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 此字段不得包含空格或其他特殊字符。  
  - **version**  
    例如： `1.0.0`  
    VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 例如，**1.0.0**。 此字段不得包含空格或其他特殊字符。  
  - **osType**  
    例如： `Linux`  
    映像的 osType 必须为 **Windows** 或 **Linux**。  
  - **OSUri**  
    例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    你可以指定 blob 存储 URI 为`osDisk`。  

    有关添加 AzsPlatformimage cmdlet 的详细信息，请参阅 PowerShell 的 Microsoft [Azure 堆栈运算符模块文档](https://docs.microsoft.com/powershell/module/)。

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>通过使用 PowerShell 将自定义 VM 映像添加到应用商店

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

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

2. 如果使用**Active Directory 联合身份验证服务**，使用以下 cmdlet:

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

3. 作为一个操作员登录到 Azure 堆栈。 有关说明，请参阅[登录到 Azure 堆栈作为一个操作员](azure-stack-powershell-configure-admin.md)。

4. 在全局 Azure 或 Azure 堆栈，来存储你的自定义 VM 映像中创建存储帐户。 有关说明，请参阅[快速入门： 上载、 下载和使用 Azure 门户的列出 blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

5. 准备 VHD 格式 (不 VHDX) 中的 Windows 或 Linux 操作系统映像、 将映像上载到你的存储帐户，并获取 PowerShell 可以其中检索 VM 映像的 URI。  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. （可选）你可以上载数组数据磁盘作为 VM 映像的一部分。 创建数据磁盘使用新建 DataDiskObject cmdlet。 从提升的提示符中，打开 PowerShell 并运行：

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. 使用提升的提示符下打开 PowerShell 并运行：

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    有关添加 AzsPlatformimage cmdlet 和新建 DataDiskObject cmdlet 的详细信息，请参阅 PowerShell 的 Microsoft [Azure 堆栈运算符模块文档](https://docs.microsoft.com/powershell/module/)。

## <a name="remove-a-vm-image-by-using-powershell"></a>使用 PowerShell 删除 VM 映像

不再需要上传的虚拟机映像时，可使用以下 cmdlet 从 Marketplace 中删除它：

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

2. 作为一个操作员登录到 Azure 堆栈。

3. 使用提升的提示符下打开 PowerShell 并运行：

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  **删除 AzsPlatformImage** cmdlet 将指定的 Azure 资源管理器模板用于引用的 VM 映像的值。 值包括：
  - **publisher**  
    例如： `Canonical`  
    VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 此字段不得包含空格或其他特殊字符。  
  - **offer**  
    例如： `UbuntuServer`  
    VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 此字段不得包含空格或其他特殊字符。  
  - **sku**  
    例如： `14.04.3-LTS`  
    VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 此字段不得包含空格或其他特殊字符。  
  - **version**  
    例如： `1.0.0`  
    VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 例如，**1.0.0**。 此字段不得包含空格或其他特殊字符。  
    
    有关 theRemove AzsPlatformImage cmdlet 的详细信息，请参阅 PowerShell 的 Microsoft [Azure 堆栈运算符模块文档](https://docs.microsoft.com/powershell/module/)。

## <a name="next-steps"></a>后续步骤

[预配虚拟机](azure-stack-provision-vm.md)