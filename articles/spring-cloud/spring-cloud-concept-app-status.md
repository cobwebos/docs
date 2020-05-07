---
title: 了解 Azure 春季云中的应用状态
description: 了解 Azure 春季云中的应用状态类别
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82568999"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>了解 Azure 春季云中的应用状态

Azure 春季云 UI 提供有关正在运行的应用程序的状态的信息。  订阅中的每个资源组都有一个**应用**选项，其中显示了应用程序类型的常规状态。  对于每种应用程序类型，都显示了**应用程序实例**。

## <a name="apps-status"></a>应用状态
若要查看应用程序类型的常规状态，请在资源组的左侧导航窗格中选择 "**应用**"。 结果显示已部署的应用的状态：

* **预配状态**显示部署的预配状态
* **正在运行的实例**显示正在运行的应用实例数/所需的应用实例数。 如果应用应停止，此列将显示 "*已停止*"。
* **已注册实例**显示了向 eureka 注册了多少应用实例，以及需要多少应用实例。 如果应用应停止，此列将显示 "*已停止*"。


 ![应用状态](media/spring-cloud-concept-app-status/apps-ui-status.png)

**部署状态将报告为以下值之一：**

| 枚举 | 定义 |
|:--:|:----------------:|
| 运行 | 部署应正在运行。 |
| 已停止 | 部署应停止。 |

**只能从 CLI 访问设置状态。 它被报告为以下值之一：**

| 枚举 | 定义 |
|:--:|:----------------:|
| Creating | 正在创建资源。 |
| 更新 | 正在更新资源。 |
| 成功 | 已成功提供资源并部署二进制文件。 |
| Failed | 未能实现*成功*目标。 |
| 正在删除 | 正在删除资源。 这会阻止操作，并且资源在此状态下不可用。 |

## <a name="app-instances-status"></a>应用程序实例状态

若要查看已部署应用的特定实例的状态，请在 "**应用**" UI 中单击应用的**名称**。 结果将显示：
* **状态**：实例是否正在运行或其状态
* **DiscoveryStatus**： Eureka server 中的应用程序实例的注册状态

 ![应用程序实例状态](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**实例状态将报告为以下值之一：**

| 枚举 | 定义 |
|:--:|:----------------:|
| 正在启动 | 该二进制文件已成功部署到给定的实例。 启动 jar 文件的实例可能会失败，因为 jar 无法正常运行。 |
| 运行 | 实例有效。 |
| Failed | 应用实例多次重试后未能启动用户的二进制文件。 |
| 因 | 正在关闭应用程序实例。 |

**实例的发现状态将报告为以下值之一：**

| 枚举 | 定义 |
|:--:|:----------------:|
| UP | 已将应用程序实例注册到 eureka 并准备好接收流量 |
| OUT_OF_SERVICE | 应用程序实例已注册到 Eureka 并且能够接收流量。 但会有意关闭流量。 |
| DOWN | 应用程序实例未注册到 Eureka 或已注册但无法接收流量。 |


## <a name="see-also"></a>另请参阅
* [准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序](spring-cloud-tutorial-prepare-app-deployment.md)