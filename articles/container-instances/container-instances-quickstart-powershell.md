---
title: 快速入门 - 在 Azure 容器实例中运行应用程序 - PowerShell
description: 本快速入门介绍如何使用 Azure PowerShell，通过 Azure PowerShell 将 Docker 容器应用程序部署到 Azure 容器实例
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e8efcbe080cc33cb6153d97d4435bcb477587980
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565847"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 在 Azure 容器实例中运行容器应用程序

使用 Azure 容器实例在 Azure 中快速方便地运行 Docker 容器。 不需要部署虚拟机或使用 Kubernetes 之类的完整容器业务流程平台。 在本快速入门中，我们将使用 Azure 门户在 Azure 中创建一个 Windows 容器，并使其应用程序可通过完全限定的域名 (FQDN) 使用。 在执行单个部署命令几秒钟之后，可以浏览到正在运行的应用程序：

![在浏览器中显示的已部署到 Azure 容器实例的应用][qs-powershell-01]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.5 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/azurerm/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

Azure 容器实例（例如所有 Azure 资源）都必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

首先，使用以下 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 命令在 *eastus* 位置创建名为 *myResourceGroup* 的资源组。

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>创建容器

创建资源组后，可在 Azure 中运行容器。 若要使用 Azure PowerShell 创建容器实例，请在 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] cmdlet 中 提供资源组名称、容器实例名称和 Docker 容器映像。 在本快速入门中，你将使用来自公共 Docker 中心注册表的 `microsoft/iis:nanoserver` Windows 映像。 此映像打包了 Internet Information Services (IIS)，可以在 Nano Server 中运行。

可以通过指定要打开的一个或多个端口、一个 DNS 名称标签（或同时指定两者）来向 Internet 公开容器。 在本快速入门中，你将部署一个具有 DNS 名称标签的容器，以便 IIS 可供公开访问。

执行以下命令以启动容器实例。 在创建实例的 Azure 区域中，`-DnsNameLabel` 值必须是唯一的。 如果收到“DNS 名称标签不可用”错误消息，请尝试使用一个不同的 DNS 名称标签。

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

在几秒钟内，应会收到来自 Azure 的响应。 容器的 `ProvisioningState` 最初为 **Creating**，但在一到两分钟内，应会改为 **Succeeded**。 使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet 检查部署状态：

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

容器的 `ProvisioningState` 变为 **Succeeded** 后，请在浏览器中导航到其 `Fqdn`。 如果看到类似于下图的网页，那么恭喜你！ 现已成功将 Docker 容器中运行的应用程序部署到 Azure。

![在浏览器中显示的使用 Azure 容器实例部署的 IIS][qs-powershell-01]

## <a name="clean-up-resources"></a>清理资源

完成容器的操作后，可使用 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] cmdlet 将其删除：

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已基于公共 Docker 中心注册表中的映像创建了 Azure 容器实例。 若要基于专用 Azure 容器注册表生成容器映像并部署它，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
