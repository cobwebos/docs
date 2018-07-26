---
title: 在 Service Fabric 网格应用程序中通过将基于 Azure 文件的卷装载到容器内存储状态 | Microsoft Docs
description: 了解如何使用 Azure CLI 在 Service Fabric 网格应用程序中通过将基于 Azure 文件的卷装载到容器内存储状态。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090626"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>在 Service Fabric 网格应用程序中通过装载基于 Azure 文件的的卷存储状态

本文介绍如何通过将卷装载到 Service Fabric 网格应用程序的容器中来存储 Azure 文件状态。 在此示例中，计数器应用程序的 ASP.NET Core 服务有一个网页，该网页在浏览器中显示计数器值。 

`counterService` 从文件定期读取计数器值，使该值递增并重新写入文件。 文件存储在由 Azure 文件共享备份的卷上装载的文件夹中。 

## <a name="set-up-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI 
可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成此任务。 根据这些[说明](service-fabric-mesh-howto-setup-cli.md)安装 Azure Service Fabric 网格 CLI 扩展模块。

## <a name="sign-in-to-azure"></a>登录 Azure
登录到 Azure 并设置订阅。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>创建文件共享 
按照这些[说明](/azure/storage/files/storage-how-to-create-file-share)操作，创建 Azure 文件共享。 在以下说明中，存储帐户名称、存储帐户密钥和文件共享名称引用为 `<storageAccountName>`、`<storageAccountKey>` 和 `<fileShareName>`。

## <a name="create-resource-group"></a>创建资源组
创建要将应用程序部署到其中的资源组。 可以使用现有资源组并跳过此步骤。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>部署模板

使用以下命令创建应用程序和相关资源，并提供上一步中的 `storageAccountName`、`storageAccountKey` 和 `fileShareName` 的值。

模板中的 `storageAccountKey` 参数是 `securestring`。 它不会显示在部署状态和 `az mesh service show` 命令中。 请确保它在以下命令中正确指定。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

上面的命令使用 [mesh_rp.linux.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [mesh_rp.windows.json 模板](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json)。 Windows 容器映像大于 Linux 容器映像，可能需要更多时间进行部署。

几分钟后，命令应返回：

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>打开应用程序
在应用程序成功部署后，获取服务终结点的公用 IP 地址并在浏览器中打开。 它显示一个网页，该网页中每秒更新计数器值。

部署命令将返回服务终结点的公共 IP 地址。 （可选）还可以通过查询网络资源来查找服务终结点的公共 IP 地址。 
 
此应用程序的网络资源名称是 `counterAppNetwork`，使用以下命令提取与其相关的信息。 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>验证应用程序能够使用卷
应用程序在 `counter/counterService` 文件夹的文件共享中创建名为 `counter.txt` 的文件。 此文件的内容是在网页上显示计数器值。

可以使用用于浏览 Azure 文件文件共享的任何工具下载文件。 [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)即是此类工具。

## <a name="delete-the-resources"></a>删除资源

为节省为预览计划分配的有限资源，请定期删除资源。 要删除与此示例相关的资源，请删除在其中部署的资源组。

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>后续步骤

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上查看 Azure 文件卷示例应用程序。
- 要详细了解 Service Fabric 资源模型，请参阅 [Service Fabric 网格资源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要详细了解 Service Fabric 网格，请阅读 [Service Fabric 网格概述](service-fabric-mesh-overview.md)。
