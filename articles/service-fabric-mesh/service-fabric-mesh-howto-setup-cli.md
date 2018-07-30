---
title: 设置 Azure Service Fabric 网格 CLI | Microsoft Docs
description: 了解如何设置 Azure Service Fabric 网格 CLI。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cf899e236b7fa21a90a0784e1e2b4fce0feae055
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213429"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI
Service Fabric 网格 CLI 是在 Service Fabric 网格中部署和管理资源所必需的。 

对于预览版，Azure Service Fabric 网格 CLI 编写为 Azure CLI 的一个扩展。 可以在 Azure Cloud Shell 中安装它，也可以在 Azure CLI 的本地安装中进行安装。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装和使用 CLI，必须安装 Azure CLI 版本 2.0.35 或更高版本。 运行 `az --version` 即可查找版本。 若要安装或升级到最新版本的 CLI，请参阅[安装 Azure CLI 2.0][azure-cli-install]。

删除以前安装的任何 Azure Service Fabric 网格 CLI 模块。

```azurecli-interactive
az extension remove --name mesh
```

使用以下命令安装 Azure Service Fabric 网格 CLI 扩展模块。 

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

还可以设置你的 [Windows 开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。

[azure-cli-install]: /cli/azure/install-azure-cli