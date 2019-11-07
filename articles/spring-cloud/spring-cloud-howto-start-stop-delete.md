---
title: 如何启动、停止和删除 Azure 春季 Cloud 应用程序 |Microsoft Docs
description: 如何启动、停止和删除 Azure 弹簧 Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607727"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>如何启动、停止和删除 Azure 弹簧 Cloud 应用程序

本指南说明如何使用 Azure 门户或 CLI 在 Azure 春季云中更改应用程序的状态。

## <a name="using-the-azure-portal"></a>使用 Azure 门户

部署应用程序后，可以使用 Azure 门户**启动**、**停止**和**删除**应用程序。

1. 在 Azure 门户中，请参阅 Azure 春季云服务实例。
1. 选择 "**应用程序仪表板**" 选项卡。
1. 选择要更改其状态的应用程序。
2. 在该应用程序的 "**概述**" 页中，找到**启动/停止**、**重新启动**和**删除**应用程序的按钮。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 可以使用可选参数，并使用 Azure CLI 配置默认值。 有关详细信息，请参阅[参考文档](spring-cloud-cli-reference.md)。  

为 Azure CLI 安装春季云扩展：

```azurecli
az extension add --name spring-cloud
```

* 若要启动应用程序：
    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 停止应用程序：
    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 重新启动应用程序：
    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 删除应用程序：
    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
