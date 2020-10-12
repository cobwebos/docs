---
title: 使用 Helm 升级入口控制器
description: 本文介绍如何使用 Helm 升级应用程序网关入口。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84806780"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>如何使用 Helm 升级应用程序网关入口控制器 

Kubernetes 的 Azure 应用程序网关入口控制器 (AGIC) 可以使用托管在 Azure 存储上的 Helm 存储库升级。

在开始升级过程之前，请确保已添加所需的存储库：

- 使用以下命令查看当前添加的 Helm 存储库：

    ```bash
    helm repo list
    ```

- 使用以下命令添加 AGIC 存储库：

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>升级

1. 刷新 AGIC Helm 存储库以获取最新版本：

    ```bash
    helm repo update
    ```

1. 查看 `application-gateway-kubernetes-ingress` 图表的可用版本：

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    示例响应：

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    在上面的列表中，最新的可用版本为 `0.7.0-rc1`

1. 查看当前安装的 Helm 图表：

    ```bash
    helm list
    ```

    示例响应：

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    上述示例响应中的 Helm 图表安装名为 `odd-billygoat`。 我们会将该名称用于其余的命令。 你的实际部署名称很可能会不同。

1. 将 Helm 部署升级到新版本：

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>回退

如果 Helm 部署失败，可以回退到以前的版本。

1. 获取上一个已知正常的发行版号：

    ```bash
    helm history odd-billygoat
    ```

    示例输出：

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    从 `helm history` 命令的示例输出中可以看到，我们的 `odd-billygoat` 的上一个成功部署是修订版 `1`

1. 回退到上一个成功的修订版：

    ```bash
    helm rollback odd-billygoat 1
    ```
