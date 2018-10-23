---
title: 设置 Azure Service Fabric 网格 CLI | Microsoft Docs
description: 了解如何设置 Azure Service Fabric 网格 CLI。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7e8a12a215c94102f6b08262f129faebf9cfcde9
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115618"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI
Service Fabric 网格命令行界面 (CLI) 是在 Service Fabric 网格中部署和管理资源所必需的。 

对于预览版，Azure Service Fabric 网格 CLI 编写为 Azure CLI 的一个扩展。 可以在 Azure Cloud Shell 中安装它，也可以在 Azure CLI 的本地安装中进行安装。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>在本地安装 Service Fabric 网格 CLI
如果选择在本地安装和使用 CLI，必须安装 Azure CLI 版本 2.0.43 或更高版本。 运行 `az --version` 即可查找版本。 若要安装或升级到最新版本的 CLI，请参阅[安装 Azure CLI][azure-cli-install]。

使用以下命令安装 Azure Service Fabric 网格 CLI 扩展模块。 

```azurecli-interactive
az extension add --name mesh
```

若要更新现有的 Azure Service Fabric 网格 CLI 模块，请运行以下命令。

```azurecli-interactive
az extension update --name mesh
```

还可以设置你的 [Windows 开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。

[azure-cli-install]: /cli/azure/install-azure-cli
