---
title: 通过将基于 Azure 文件的卷装载到容器，在 Azure Service Fabric 网格应用程序中存储状态 | Microsoft Docs
description: 了解如何使用 Azure CLI，通过将基于 Azure 文件的卷装载到容器，在 Azure Service Fabric 网格应用程序中存储状态。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037902"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>通过将基于 Azure 文件的卷装载到容器，在 Azure Service Fabric 网格应用程序中存储状态

本文介绍如何通过将卷装载到 Service Fabric 网格应用程序的容器中来存储 Azure 文件状态。 在此示例中，计数器应用程序的 ASP.NET Core 服务有一个网页，该网页在浏览器中显示计数器值。 

`counterService` 定期从文件读取计数器值，使该值递增并重新写入文件。 文件存储在由 Azure 文件共享备份的卷上装载的文件夹中。

## <a name="prerequisites"></a>先决条件

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成此任务。 若要在本文中使用 Azure CLI，请确保 `az --version` 至少返回 `azure-cli (2.0.43)`。  遵照这些[说明](service-fabric-mesh-howto-setup-cli.md)安装（或更新）Azure Service Fabric 网格 CLI 扩展模块。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure 并设置订阅。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>创建文件共享

按照这些[说明](/azure/storage/files/storage-how-to-create-file-share)操作，创建 Azure 文件共享。 在以下说明中，存储帐户名称、存储帐户密钥和文件共享名称引用为 `<storageAccountName>`、`<storageAccountKey>` 和 `<fileShareName>`。 Azure 门户中提供了这些值：
* <storageAccountName> - 位于“存储帐户”下，它是在创建文件共享时使用的存储帐户的名称。
* <storageAccountKey> - 在“存储帐户”下选择自己的存储帐户，然后选择“访问密钥”并使用“密钥 1”下面的值。
* <fileShareName> - 在“存储帐户”下选择自己的存储帐户，然后选择“文件”。 要使用的名称是刚刚创建的文件共享的名称。

## <a name="create-a-resource-group"></a>创建资源组

创建要将应用程序部署到其中的资源组。 以下命令在美国东部位置创建名为 `myResourceGroup` 的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>部署模板

使用以下命令创建应用程序和相关资源，并提供前面的[创建文件共享](#create-a-file-share)步骤中提到的 `storageAccountName`、`storageAccountKey` 和 `fileShareName` 值。

模板中的 `storageAccountKey` 参数是一个安全字符串。 它不会显示在部署状态和 `az mesh service show` 命令中。 请确保它在以下命令中正确指定。

以上命令使用 [mesh_rp.linux.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [mesh_rp.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)。 请注意，较大的容器映像可能需要花费较长的时间进行部署。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

几分钟后，命令应返回 `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>打开应用程序

部署命令将返回服务终结点的公共 IP 地址。 应用程序成功部署后，获取服务终结点的公共 IP 地址并在浏览器中将其打开。 它会显示一个网页，其中的计数器值每秒更新。

此应用程序的网络资源名称为 `counterAppNetwork`。 可使用以下命令查看此应用的相关信息，例如其说明、位置、资源组等等：

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>验证应用程序能够使用卷

应用程序在 `counter/counterService` 文件夹的文件共享中创建名为 `counter.txt` 的文件。 此文件的内容是在网页上显示计数器值。

可以使用用于浏览 Azure 文件文件共享的任何工具（例如 [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）下载此文件。

## <a name="delete-the-resources"></a>删除资源

请经常删除 Azure 中不再使用的资源。 若要删除与此示例相关的资源，请使用以下命令删除其中部署了它们的资源组（这会删除与该资源组关联的所有内容）：

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上查看 Azure 文件卷示例应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。