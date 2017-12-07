---
title: "快速入门 - 使用 PowerShell 创建首个 Azure 容器实例容器"
description: "通过使用 PowerShell 创建 Windows 容器实例，可开始使用 Azure 容器实例。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: f24619c9ff2667bb96827b1f17bf9ff04b42eaff
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>在 Azure 容器实例中创建你的第一个容器

使用 Azure 容器实例，可以在 Azure 中轻松创建和管理 Docker 容器，无需预配虚拟机或采用更高级别的服务。

在此快速入门中，将在 Azure 中创建 Windows 容器，然后通过公共 IP 地址向 Internet 公开该容器。 此操作通过单个命令完成。 就在几分钟之内，便可在浏览器中看到正在运行的应用程序：

![在浏览器中显示的使用 Azure 容器实例部署的应用][qs-powershell-01]

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 3.6 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>创建容器

在 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] cmdlet 中提供名称、Docker 映像和 Azure 资源组可创建容器。 可以选择通过公共 IP 地址向 Internet 公开容器。 在此情况下，我们将使用运行 Internet Information Services (IIS) 的 Windows Nano Server 容器。

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

在数秒内，便可获得请求的响应。 容器一开始处于“正在创建”状态，但会在一两分钟内启动。 可以使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet 检查状态：

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的预配状态和 IP 地址会显示在 cmdlet 的输出中：

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

在容器的 **ProvisioningState** 变成 `Succeeded` 后，即可在浏览器中使用所提供的 IP 地址来访问它。

![在浏览器中显示的使用 Azure 容器实例部署的 IIS][qs-powershell-01]

## <a name="delete-the-container"></a>删除容器

完成容器的操作后，可使用 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] cmdlet 将其删除：

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，已在 Azure 容器实例中启动预建的 Windows 容器。 如果要尝试使用 Azure 容器注册表来自行生成容器并将其部署到 Azure 容器实例，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png