---
title: 升级入口控制器与 Helm
description: 本文介绍如何使用 Helm 升级应用程序网关入口。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795906"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>如何使用 Helm 升级应用程序网关入口控制器 

可以使用托管在 Azure 存储上的 Helm 存储库来升级适用于 Kubernetes （AGIC）的 Azure 应用程序网关入口控制器。

在开始升级过程之前，请确保已添加所需的存储库：

- 查看当前添加的 Helm 存储库：

    ```bash
    helm repo list
    ```

- 添加 AGIC 存储库：

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

    以上列表中的最新可用版本为： `0.7.0-rc1`

1. 查看当前已安装的 Helm 图表：

    ```bash
    helm list
    ```

    示例响应：

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    上面的示例响应中的 Helm 图安装名为 `odd-billygoat`。 对于其余的命令，我们将使用此名称。 实际的部署名称最有可能不同。

1. 将 Helm 部署升级到新版本：

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>回退

如果 Helm 部署失败，则可以回滚到以前的版本。

1. 获取上一个已知的正常发行版号码：

    ```bash
    helm history odd-billygoat
    ```

    示例输出：

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    在 `helm history` 命令的示例输出中，它看起来就像是最后一次成功部署 `odd-billygoat` 的修订版 `1`

1. 回滚到上次成功的修订：

    ```bash
    helm rollback odd-billygoat 1
    ```
