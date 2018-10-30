---
title: 从 VHD 部署 Azure 市场 VM | Microsoft Docs
description: 介绍如何从 Azure 部署的 VHD 注册 VM。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638873"
---
# <a name="deploy-a-vm-from-your-vhds"></a>从 VHD 部署 VM

本文介绍如何从 Azure 部署的虚拟硬盘 (VHD) 注册虚拟机 (VM)。  其中列出了所需的工具，以及如何使用这些工具创建用户 VM 映像，然后使用 [Microsoft Azure 门户](https://ms.portal.azure.com/)或 PowerShell 脚本将其部署到 Azure。 

将虚拟硬盘 (VHD)（通用化的操作系统 VHD 以及零个或多个数据磁盘 VHD）上传到 Azure 存储帐户之后，可将其注册为用户 VM 映像。 然后可以测试该映像。 由于操作系统 VHD 已通用化，因此无法通过提供 VHD URL 来直接部署 VM。

若要详细了解 VM 映像，请参阅以下博客文章：

- [VM 映像](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)（VM 映像 PowerShell 操作方法）


## <a name="set-up-the-necessary-tools"></a>设置所需的工具

遵照以下说明安装 Azure PowerShell 和 Azure CLI（如果尚未这样做）：

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>创建用户 VM 映像

接下来，从通用化的 VHD 创建非托管映像。

#### <a name="capture-the-vm-image"></a>捕获 VM 映像

遵照以下文章中的说明捕获对应于访问方法的 VM：

-  PowerShell：[如何从 Azure VM 创建非托管 VM 映像](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI：[如何创建虚拟机或 VHD 的映像](../../../virtual-machines/linux/capture-image.md)
-  API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>通用化 VM 映像

由于用户映像是从以前已通用化的 VHD 生成的，因此也应该通用化该映像。  同样，请选择对应于访问机制中的以下文章。  （捕获磁盘时可能已将其通用化。）

-  PowerShell：[通用化 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI：[步骤 2：创建 VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API：[虚拟机 - 通用化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>从用户 VM 映像部署 VM

接下来，使用 Azure 门户或 PowerShell 从用户 VM 映像部署 VM。

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>从 Azure 门户部署 VM

在 Azure 门户中使用以下过程部署用户 VM。

1.  登录到 [Azure 门户](https://portal.azure.com)。

2.  单击“新建”并搜索“模板部署”，然后选择“在编辑器中生成自己的模板”。  <br/>
  ![在 Azure 门户中生成 VHD 部署模板](./media/publishvm_021.png)

3. 复制此 [JSON 模板](./cpp-deploy-json-template.md)并将其粘贴到编辑器中，然后单击“保存”。 <br/>
  ![在 Azure 门户中保存 VHD 部署模板](./media/publishvm_022.png)

4. 提供显示的“自定义部署”属性页的参数值。

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parameter**              |   **说明**                                                            |
   |  -------------              |   ---------------                                                            |
   | 用户存储帐户名称   | 通用化 VHD 所在的存储帐户名称                    |
   | 用户存储容器名称 | 通用化 VHD 所在的容器名称                          |
   | 公共 IP 的 DNS 名称      | 公共 IP DNS 名称                                                           |
   | 管理员用户名             | 新 VM 的管理员帐户的用户名                                  |
   | 管理员密码              | 新 VM 的管理员帐户的密码                                  |
   | OS 类型                     | VM 操作系统：`Windows` \| `Linux`                                    |
   | 订阅 ID             | 所选订阅的标识符                                      |
   | 位置                    | 部署的地理位置                                        |
   | VM 大小                     | [Azure VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如 `Standard_A2` |
   | 公共 IP 地址名称      | 公共 IP 地址的名称                                               |
   | VM 名称                     | 新 VM 的名称                                                           |
   | 虚拟网络名称        | VM 使用的虚拟网络的名称                                   |
   | NIC 名称                    | 运行虚拟网络的网络接口卡的名称               |
   | VHD URL                     | 完整的 OS 磁盘 VHD URL                                                     |
   |  |  |
            
5. 提供这些值后，单击“购买”。 

Azure 将开始部署：使用指定的非托管 VHD 在指定的存储帐户路径中创建新 VM。  可以单击 Azure 门户左侧的“虚拟机”，在门户中跟踪进度。  创建 VM 后，状态将从 `Starting` 更改为 `Running`。 


### <a name="deploy-a-vm-from-powershell"></a>从 PowerShell 部署 VM

若要从刚刚创建的通用化 VM 映像部署大型 VM，请使用以下 cmdlet。

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>后续步骤

部署 VM 后，可以[配置 VM](./cpp-configure-vm.md)。
