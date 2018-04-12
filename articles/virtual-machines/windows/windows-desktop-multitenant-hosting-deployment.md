---
title: 如何使用多租户托管权限在 Azure 上部署 Windows 10
description: 了解如何充分利用 Windows 软件保障权益将本地许可证引入到 Azure 中
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 5952c602a90568a9ce9e71dfa2c0dd383aed4e16
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>如何使用多租户托管权限在 Azure 上部署 Windows 10 
对于其用户使用 Windows 10 企业版 E3/E5 或使用 Windows 虚拟桌面访问（用户订阅许可证或附加设备用户订阅许可证）的客户，通过使用 Windows 10 多租户托管权限，他们可以在云中使用其 Windows 10 许可证并在 Azure 上运行 Windows 10 虚拟机，无需购买其他许可证。 有关详细信息，请参阅 [Multitenant Hosting for Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)（Windows 10 多租户托管）。

> [!NOTE]
> 本文演示如何在 Azure Marketplace 上实现 Windows 10 专业版桌面映像的许可权益。
> - 有关 Azure Marketplace 上 MSDN 订阅的 Windows 7、Windows 8.1、Windows 10 企业版 (x64) 映像，请参阅 [Azure 中用于开发/测试方案的 Windows 客户端](client-images.md)
> - 有关 Windows Server 许可权益，请参阅 [Windows Server 映像的 Azure 混合使用权益](hybrid-use-benefit-licensing.md)。
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>通过 Azure Marketplace 部署 Windows 10 映像 
对于 Powershell、CLI 和 Azure 资源管理器模板部署，可使用以下 publishername、产品/服务及 sku 找到 Windows 10 映像。

| 操作系统  |      PublisherName      |  产品 | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 专业版    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 专业版 N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 专业版    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 专业版 N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>将 Windows 10 VHD 上传到 Azure
如果要上传通用化的 Windows 10 VHD，请注意，Windows 10 不会默认启用内置 Administrator 帐户。 若要启用内置 Administrator 帐户，请在自定义脚本扩展中包含以下命令。

```powershell
Net user <username> /active:yes
```

以下 powershell 代码片段用于将所有管理员（包括内置 Administrator）帐户标记为活动帐户。 如果内置 Administrator 用户名未知，此示例非常有用。
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
更多相关信息： 
* [如何将 VHD 上传到 Azure](upload-generalized-managed.md)
* [如何准备好要上传到 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>使用多租户托管权限部署 Windows 10
确保[已安装并配置最新的 Azure PowerShell](/powershell/azure/overview)。 准备好 VHD 之后，即可使用 `Add-AzureRmVhd` cmdlet 将 VHD 上传到 Azure 存储帐户，如下所示：

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**使用 Azure 资源管理器模板部署进行部署** 在资源管理器模板中，可为 `licenseType` 指定一个附加参数。 可以阅读有关[创作 Azure 资源管理器模板](../../resource-group-authoring-templates.md)的详细信息。 将 VHD 上传到 Azure 之后，请编辑 Resource Manager 模板以将许可证类型包含为计算提供程序的一部分，然后照常部署模板：
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

**通过 PowerShell 部署** 通过 PowerShell 部署 Windows Server VM 时，可使用 `-LicenseType` 的附加参数。 将 VHD 上传到 Azure 之后，可以使用 `New-AzureRmVM` 创建 VM 并指定许可类型，如下所示：
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>验证 VM 是否正在利用许可权益
通过 PowerShell 或 Resource Manager 部署方法部署 VM 之后，请使用 `Get-AzureRmVM` 验证许可证类型，如下所示：
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

输出类似于许可证类型正确的 Windows 10 的以下示例：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

以下 VM 在部署时未提供 Azure 混合使用权益许可（例如直接从 Azure 库部署 VM），可看出其中的输出差异：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>有关联接 Azure AD 的其他信息
>[!NOTE]
>Azure 使用内置 Administrator 帐户预配所有 Windows，但不能使用此方法联接 AAD。 例如，“设置”>“帐户”>“访问工作或学校帐户”>“+连接”将不起作用。 若要手动加入 Azure AD，必须创建另一个管理员帐户并以其身份登录。 还可以使用预配包配置 Azure AD，使用“后续步骤”部分中的链接了解详细信息。
>
>

## <a name="next-steps"></a>后续步骤
- 深入了解[为 Windows 10 配置 VDA](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)
- 深入了解 [Windows 10 的多租户托管](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)


