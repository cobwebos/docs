---
title: 如何升级 Azure Dev Spaces 工具
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
description: 了解如何升级 Azure Dev Spaces 命令行工具、Visual Studio Code 扩展和 Visual Studio 扩展
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.openlocfilehash: 1dad455b834bbef046b295b2cba040831a74f757
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873483"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>如何升级 Azure Dev Spaces 工具

如果有新版本并且你已在使用 Azure Dev Spaces，则可能需要升级 Azure Dev Spaces 客户端工具。

## <a name="update-the-azure-cli"></a>更新 Azure CLI

更新最新的 Azure CLI 时，还可以获取最新版本的 Dev Spaces CLI 扩展。

无需卸载以前的版本，只需在 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 查找相应的下载。


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>更新 Dev Spaces CLI 扩展和命令行工具

运行以下命令：

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>更新 VS Code 扩展

安装后，该扩展将自动更新。 可能需要重新加载扩展才可以使用新功能。 在 VS Code 中，打开“扩展”窗格，选择“Azure Dev Spaces”扩展，然后选择“重新加载”。

## <a name="update-visual-studio"></a>更新 Visual Studio

Azure Dev Spaces 是 Azure 开发工作负载的一部分，包含在所有 Visual Studio 更新中。

## <a name="next-steps"></a>后续步骤

通过创建新群集来测试新工具。 在 [Azure Dev Spaces](/azure/dev-spaces) 尝试快速入门和教程。
