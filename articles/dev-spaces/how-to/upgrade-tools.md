---
title: 如何升级 Azure Dev Spaces 工具 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/03/2018
ms.topic: article
ms.technology: azds-kubernetes
description: 在 Azure 中使用容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
manager: douge
ms.openlocfilehash: bcf36e14a12119dd13b8e566bc0a5b0ac96925c5
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934119"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>如何升级 Azure Dev Spaces 工具

如果有新版本并且你已在使用 Azure Dev Spaces，则可能需要升级 Azure Dev Spaces 客户端工具。

## <a name="update-the-azure-cli"></a>更新 Azure CLI

更新最新的 Azure CLI 时，还可以获取最新版本的 Dev Spaces CLI 扩展。

无需卸载以前的版本，只需在 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 查找相应的下载。


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>更新 Dev Spaces CLI 扩展和命令行工具

运行以下命令：

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>更新 VS Code 扩展

安装后，该扩展将自动更新。 可能需要重新加载扩展才可以使用新功能。 在 VS Code 中，打开“扩展”窗格，选择“Azure Dev Spaces”扩展，然后选择“重新加载”。

## <a name="update-the-visual-studio-extension"></a>更新 Visual Studio 扩展

与其他扩展和更新一样，Visual Studio 将在出现适用于 Visual Studio Tools for Kubernetes（包括 Azure Dev Spaces）的更新时通知你。 查找屏幕右上角的标志图标。

若要更新 Visual Studio 中的工具，请选择“工具 > 扩展和更新”菜单项，并在左侧选择“更新”。 查找“Visual Studio Tools for Kubernetes” ，然后选择“更新”按钮。

## <a name="next-steps"></a>后续步骤

通过创建新群集来测试新工具。 在 [Azure Dev Spaces](/azure/dev-spaces) 尝试快速入门和教程。

> [!WARNING]
> 不会立即修补现有群集上的 Azure Dev Spaces，因此要确保在所有 Azure 部署上使用最新版本，并在升级工具后创建新群集。
