---
title: 快速入门 - 将 Docker 容器部署到 Azure 容器实例 - PowerShell
description: 本快速入门将使用 Azure PowerShell 快速部署在隔离的 Azure 容器实例中运行的容器化 Web 应用
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7fe199d2ac228ddb0ccfd1e5bc980e680e160acf
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325828"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 在 Azure 中部署容器实例

使用 Azure 容器实例在 Azure 中快速方便地运行无服务器 Docker 容器。 当你不需要像 AzureKubernetes 服务这样的完整容器业务流程平台时，可以按需将应用程序部署到容器实例。

本快速入门将使用 Azure PowerShell 部署一个独立的 Windows 容器，并使其应用程序可通过完全限定的域名 (FQDN) 使用。 在执行单个部署命令几秒钟之后，可以浏览到正在容器中运行的应用程序：

![在浏览器中显示的已部署到 Azure 容器实例的应用][qs-powershell-01]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

Azure 容器实例（例如所有 Azure 资源）都必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

首先，使用以下 [New-AzResourceGroup][New-AzResourceGroup] 命令在 eastus 位置创建名为 myResourceGroup 的资源组   ：

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>创建容器

创建资源组后，可在 Azure 中运行容器。 若要使用 Azure PowerShell 创建容器实例，请在 [New-AzContainerGroup][New-AzContainerGroup] cmdlet 中提供资源组名称、容器实例名称和 Docker 容器映像。 本快速入门将使用公共 `mcr.microsoft.com/windows/servercore/iis:nanoserver` 映像。 此映像打包了 Microsoft Internet Information Services (IIS)，以在 Nano Server 中运行。

可以通过指定要打开的一个或多个端口、一个 DNS 名称标签（或同时指定两者）来向 Internet 公开容器。 在本快速入门中，你将部署一个具有 DNS 名称标签的容器，以便 IIS 可供公开访问。

执行类似于以下的命令以启动容器实例。 设置在创建实例的 Azure 区域中唯一的 `-DnsNameLabel` 值。 如果收到“DNS 名称标签不可用”错误消息，请尝试使用一个不同的 DNS 名称标签。

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

在几秒钟内，应会收到来自 Azure 的响应。 容器的 `ProvisioningState` 最初为 **Creating**，但在一到两分钟内，应会改为 **Succeeded**。 使用 [Get-AzContainerGroup][Get-AzContainerGroup] cmdlet 检查部署状态：

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的预配状态、完全限定的域名 (FQDN) 和 IP 地址会显示在 cmdlet 的输出中：

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


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

使用完容器后，可使用 [Remove-AzContainerGroup][Remove-AzContainerGroup] cmdlet 将其删除：

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已基于公共 Docker 中心注册表中的映像创建了 Azure 容器实例。 若要基于专用 Azure 容器注册表生成容器映像并部署它，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
