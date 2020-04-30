---
title: 教程 - 删除 Azure Red Hat OpenShift 群集
description: 在本教程中，了解如何使用 Azure CLI 删除 Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232132"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>教程：删除 Azure Red Hat OpenShift 4 群集

在本教程的第三部分中，将删除运行 OpenShift 4 的 Azure Red Hat OpenShift 群集。 学习如何：

> [!div class="checklist"]
> * 删除 Azure Red Hat OpenShift 群集


## <a name="before-you-begin"></a>在开始之前

在前面的教程中，使用 OpenShift web 控制台创建并连接了 Azure Red Hat OpenShift 群集。 如果尚未执行这些步骤，并且想要继续操作，请从[教程 1-创建 Azure Red Hat Openshift 4 群集开始。](tutorial-create-cluster.md)

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 版本2.0.75 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="sign-in-to-azure"></a>登录 Azure

如果在本地运行 Azure CLI，请运行 `az login` 以登录到 Azure。

```bash
az login
```

如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

## <a name="delete-the-cluster"></a>删除群集

在前面的教程中，已设置以下变量。 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

使用这些值删除群集：

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

系统将提示你确认是否要删除群集。 通过 `y` 进行确认后，删除群集需要几分钟。 命令完成后，整个资源组以及其中的所有资源（包括群集）都将被删除。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 删除 Azure Red Hat OpenShift 4 群集

详细了解如何将 OpenShift 与官方 [Red Hat OpenShift 文档](https://www.openshift.com/try)结合使用
