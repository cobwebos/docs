---
title: "特定于应用程序或用户的 Marathon 服务 | Microsoft Docs"
description: "创建特定于应用程序或用户的 Marathon 服务"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, Marathon, 微服务, DC/OS, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 3134872eb59f2f6219499f3d5a92673f680af04d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="create-an-application-or-user-specific-marathon-service"></a>创建特定于应用程序或用户的 Marathon 服务
Azure 容器服务提供了一组主服务器，我们可以在上面预配置 Apache Mesos 和 Marathon。 这些服务可用于在群集上安排应用程序，但最好不要将主服务器用于此目的。 例如，调整 Marathon 的配置需要登录到主服务器本身并执行更改，该过程鼓励使用与标准稍有不同并需要单独监控和管理的唯一主服务器。 此外，一个团队所需的配置可能并不是另一个团队的最佳配置。

在本文中，我们介绍如何添加特定于应用程序或用户的 Marathon 服务。

因为此服务将属于单个用户或团队，他们可以按照所需的任何方式自由配置。 此外，Azure 容器服务还要确保该服务将能继续运行。 如果服务出现故障，Azure 容器服务会重新启动它。 大多数情况下，甚至不会注意到有停机时间。

## <a name="prerequisites"></a>先决条件
使用 orchestrator 类型 DC/OS [部署 Azure 容器服务实例](container-service-deployment.md)，并[确保客户端可以连接到群集](../container-service-connect.md)。 还要执行以下步骤。

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>创建特定于应用程序或用户的 Marathon 服务
首先创建 JSON 配置文件，该文件定义要创建的应用程序服务的名称。 在此，我们将 `marathon-alice` 用作框架名称。 将文件保存为类似如下的 `marathon-alice.json`：

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

接下来，使用 DC/OS CLI 安装包含有在配置文件中设置的选项的 Marathon 实例：

```bash
dcos package install --options=marathon-alice.json marathon
```

现在，应看到正在 DC/OS UI 的“服务”选项卡中运行的 `marathon-alice`。 如果想要直接访问该文件，UI 将为 `http://<hostname>/service/marathon-alice/`。

## <a name="set-the-dcos-cli-to-access-the-service"></a>设置 DC/OS CLI 以访问服务
可以根据需要配置 DC/OS CLI，通过将 `marathon.url` 属性设置为指向 `marathon-alice` 实例访问这项新服务，如下所示：

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

可以通过 `dcos config show` 命令验证 CLI 针对哪个 Marathon 实例运行。 可以通过命令 `dcos config unset marathon.url`返回使用 Marathon 主服务。


