---
title: 如何启动、停止和删除 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 如何启动、停止和删除 Azure Spring Cloud 应用程序
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038436"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>如何启动、停止和删除 Azure Spring Cloud 应用程序

本指南说明如何使用 Azure 门户或 CLI 在 Azure Spring Cloud 中更改应用程序的状态。

## <a name="using-the-azure-portal"></a>使用 Azure 门户

部署应用程序后，可以使用 Azure 门户**启动**、**停止**和**删除**应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务实例。
1. 选择“应用程序仪表板”  选项卡。
1. 选择要更改其状态的应用程序。
2. 在该应用程序的“概述”  页中，找到“启动/停止”  、“重启”  和“删除”  应用程序的按钮。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 可以使用可选参数，并使用 Azure CLI 配置默认值。 通过阅读我们的[参考文档](spring-cloud-cli-reference.md)了解更多信息。

* 若要启动应用程序：
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要停止应用程序：
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要重启应用程序：
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要删除应用程序：
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
