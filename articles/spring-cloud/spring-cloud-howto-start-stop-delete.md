---
title: 启动、停止和删除 Azure 春云应用程序 |微软文档
description: 如何启动、停止和删除 Azure Spring Cloud 应用程序
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276826"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>启动、停止和删除 Azure 春云应用程序

本指南介绍如何使用 Azure 门户或 Azure CLI 更改应用程序在 Azure 春云中的状态。

## <a name="using-the-azure-portal"></a>使用 Azure 门户

部署应用程序后，可以使用 Azure 门户启动、停止和删除应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务实例。
1. 选择“应用程序仪表板”**** 选项卡。
1. 选择要更改其状态的应用程序。
1. 在该应用程序的 **"概述"** 页上，选择 **"开始/停止**"、"**重新启动**"或 **"删除**"。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 可以使用可选参数，并使用 Azure CLI 配置默认值。 通过阅读[我们的参考文档](spring-cloud-cli-reference.md)，了解有关 Azure CLI 的更多。  

首先，为 Azure CLI 安装 Azure 春云扩展，如下所示：

```azurecli
az extension add --name spring-cloud
```

接下来，选择以下任一 Azure CLI 操作：

* 若要启动应用程序：

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要停止应用程序：

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要重启应用程序：

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要删除应用程序：

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
