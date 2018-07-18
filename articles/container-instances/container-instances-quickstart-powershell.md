---
title: 快速入门 - 使用 PowerShell 创建首个 Azure 容器实例容器
description: 在本快速入门中，我们将使用 Azure PowerShell 在 Azure 容器实例中部署一个 Windows 容器
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453560"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入门：在 Azure 容器实例中创建第一个容器

使用 Azure 容器实例，可以在 Azure 中轻松创建和管理 Docker 容器，无需预配虚拟机或采用更高级别的服务。 在本快速入门中，我们将在 Azure 中创建一个 Windows 容器，并使用完全限定的域名 (FQDN) 向 Internet 公开此容器。 此操作通过单个命令完成。 就在几分钟之内，便可在浏览器中看到正在运行的应用程序：

![在浏览器中显示的使用 Azure 容器实例部署的应用][qs-powershell-01]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.5 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>创建容器

在 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] cmdlet 中提供名称、Docker 映像和 Azure 资源组可创建容器。 可以选择性地使用 DNS 名称标签向 Internet 公开容器。

执行以下命令，启动运行 Internet Information Services (IIS) 的 Nano Server 容器。 `-DnsNameLabel` 值必须在创建实例时所在的 Azure 区域中唯一，因此可能需要对此值进行修改，以确保唯一性。

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

在数秒内应该就会收到请求的响应。 容器一开始处于“正在创建”状态，但会在一两分钟内启动。 可以使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet 检查部署状态：

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的预配状态、完全限定的域名 (FQDN) 和 IP 地址会显示在 cmdlet 的输出中：

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

容器的 **ProvisioningState** 变成 `Succeeded` 后，请在浏览器中导航到容器的 `Fqdn`：

![在浏览器中显示的使用 Azure 容器实例部署的 IIS][qs-powershell-01]

## <a name="clean-up-resources"></a>清理资源

完成容器的操作后，可使用 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] cmdlet 将其删除：

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已基于公共 Docker 中心注册表中的映像创建了 Azure 容器实例。 若要亲自基于专用 Azure 容器注册表生成容器映像并将其部署到 Azure 容器实例，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
