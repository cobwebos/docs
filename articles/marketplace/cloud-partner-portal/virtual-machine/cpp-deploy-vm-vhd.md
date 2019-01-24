---
title: 从 VHD 部署 Azure 市场 VM | Microsoft Docs
description: 介绍如何从 Azure 部署的 VHD 注册 VM。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 3ab98669e01c9cfb2d4f46b8ddd83ff69653337b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434166"
---
# <a name="deploy-a-vm-from-your-vhds"></a>从 VHD 部署 VM

本部分介绍如何从 Azure 部署的虚拟硬盘 (VHD) 部署虚拟机 (VM)。  其中列出了所需的工具，以及如何使用这些工具创建用户 VM 映像，然后使用 PowerShell 脚本将其部署到 Azure。

将虚拟硬盘 (VHD)（通用化的操作系统 VHD 以及零个或多个数据磁盘 VHD）上传到 Azure 存储帐户之后，可将其注册为用户 VM 映像。 然后可以测试该映像。 由于操作系统 VHD 已通用化，因此无法通过提供 VHD URL 来直接部署 VM。

若要详细了解 VM 映像，请参阅以下博客文章：

- [VM 映像](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell 'How To'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)（VM 映像 PowerShell 操作方法）


## <a name="prerequisite-install-the-necessary-tools"></a>先决条件：安装必要的工具

遵照以下说明安装 Azure PowerShell 和 Azure CLI（如果尚未这样做）：

- [使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
- [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>部署步骤

将使用以下步骤来创建和部署用户 VM 映像：

1. 创建用户 VM 映像，这需要捕获和通用化映像。 
2. 创建证书并将其存储在新的 Azure Key Vault 中。 建立与 VM 的安全 WinRM 连接需要此证书。  提供了 Azure 资源管理器模板和 Azure PowerShell 脚本。 
3. 使用提供的模板和脚本从用户 VM 映像部署 VM。

部署 VM 后，即可[认证 VM 映像](./cpp-certify-vm.md)。

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


## <a name="next-steps"></a>后续步骤

接下来，将为解决方案[创建用户 VM 映像](cpp-create-user-image.md)。

