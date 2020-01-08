---
title: Service Fabric 具有 Service Fabric 网格的可靠磁盘卷
description: 了解如何使用 Azure CLI，通过将基于 Service Fabric Reliable Disk 的卷装载到容器，在 Azure Service Fabric 网格应用程序中存储状态。
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497968"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>在 Service Fabric 网格应用程序中装载高度可用的基于 Service Fabric Reliable Disk 的卷 
使用容器应用保存状态的常用方法是使用远程存储，例如 Azure 文件存储或 Azure Cosmos DB 等数据库。 这会对远程存储造成长时间读取和写入网络延迟。

本文介绍如何通过将卷装载到 Service Fabric 网格应用程序的容器以在高度可用的 Service Fabric Reliable Disk 中存储状态。
Service Fabric Reliable Disk 为 Service Fabric 群集中复制的本地读取和写入操作提供卷以实现高可用性。 这会删除读取的网络调用并降低写入的网络延迟。 如果容器重新启动或移动到另一个节点，新容器实例会将同一个卷视为旧卷。 因此，它既高效又高度可用。

在此示例中，计数器应用程序的 ASP.NET Core 服务有一个网页，该网页在浏览器中显示计数器值。

`counterService` 定期从文件读取计数器值，使该值递增并重新写入文件。 文件存储在由 Service Fabric Reliable Disk 备份的卷上装载的文件夹中。

## <a name="prerequisites"></a>必备组件

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成此任务。 若要在本文中使用 Azure CLI，请确保 `az --version` 至少返回 `azure-cli (2.0.43)`。  遵照这些[说明](service-fabric-mesh-howto-setup-cli.md)安装（或更新）Azure Service Fabric 网格 CLI 扩展模块。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 并设置订阅。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>创建资源组

创建要将应用程序部署到其中的资源组。 以下命令在美国东部位置创建名为 `myResourceGroup` 的资源组。 如果更改以下命令中的资源组名称，请记得在之后的所有命令中进行更改。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>部署模板

以下命令使用 [counter.sfreliablevolume.linux.json 模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [counter.sfreliablevolume.windows.json 模板](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)。 请注意，较大的容器映像可能需要花费较长的时间进行部署。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

还可以使用以下命令查看部署的状态

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

请注意资源类型为 `Microsoft.ServiceFabricMesh/gateways` 的网关资源的名称。 这将用于获取应用的公共 IP 地址。

## <a name="open-the-application"></a>打开应用程序

成功部署应用程序后，将获取应用的网关资源的 IP 地址。 使用上述部分中提到的网关名称。
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

输出应具有属性 `ipAddress`，即服务终结点的公共 IP 地址。 在浏览器中打开它。 它会显示一个网页，其中的计数器值每秒更新。

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>验证应用程序能够使用卷

应用程序在 `counter/counterService` 文件夹的卷中创建名为 `counter.txt` 的文件。 此文件的内容是在网页上显示计数器值。

## <a name="delete-the-resources"></a>删除资源

请经常删除 Azure 中不再使用的资源。 若要删除与此示例相关的资源，请使用以下命令删除其中部署了它们的资源组（这会删除与该资源组关联的所有内容）：

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上查看 Service Fabric Reliable Volume Disk 示例应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
