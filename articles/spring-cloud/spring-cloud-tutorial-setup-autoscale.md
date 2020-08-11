---
title: 教程 - 为微服务应用程序设置自动缩放功能
description: 本文介绍如何适用 Microsoft Azure 门户或 Azure CLI 中为应用程序设置自动缩放设置。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6e4494762c4652d3db66940dddebb13c3ab03af3
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449378"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>教程：为微服务应用程序设置自动缩放功能
自动缩放是 Azure Spring Cloud 的一种内置功能，可帮助微服务应用程序在需求更改时发挥最佳性能。 这包括修改虚拟 CPU、内存和应用实例的数量。 本文介绍如何适用 Microsoft Azure 门户或 Azure CLI 中为应用程序设置自动缩放设置。

## <a name="prerequisites"></a>先决条件

若要遵循这些过程进行操作，需要：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 部署的 Azure Spring Cloud 服务实例。 按[有关如何通过 Azure CLI 来部署应用的快速入门](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)中的说明开始操作。
* 至少已在该服务实例中创建一个应用程序。

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>导航到 Azure 门户中的“自动缩放”页

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 转到 Azure Spring Cloud 的“概览”页。
3. 选择包含服务的资源组。
4. 在左侧导航窗格上菜单中的“设置”下选择“应用”选项卡。 
5. 选择要为其设置自动缩放功能的应用程序。 在此示例中，选择名为 demo 的应用程序。 然后会看到应用程序的“概览”页。
6. 转到左侧导航窗格上菜单中的“设置”下的“横向扩展”选项卡。 
7. 选择要设置自动缩放功能的部署。 你应会看到以下部分中所示的自动缩放选项。


![自动缩放菜单](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>在 Azure 门户中为应用程序设置自动缩放设置

自动缩放需求管理有两个选项：

* 手动缩放：保持固定的实例计数。 在“标准”层中，最多可横向扩展到 500 个实例。 此值更改微服务应用程序的单独运行实例的数目。
* 自定义自动缩放：根据任何指标按任何计划进行缩放。

在 Azure 门户中，选择所需缩放方式。  下图显示“自定义自动缩放”选项和模式设置。

![自定义自动缩放](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>在 Azure CLI 中为应用程序设置自动缩放设置
还可以使用 Azure CLI 设置自动缩放模式。  以下命令创建自动缩放设置和自动缩放规则。

* 创建自动缩放设置
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* 创建自动缩放规则
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>升级到标准层

如果在使用基本层，且受其中一项或多项限制约束，则可升级到标准层。 若要执行此操作，请先选择“标准层”列，然后单击“升级”按钮，转到“定价层”菜单。

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 自动缩放概述](https://docs.microsoft.com/azure/azure-monitor/platform/autoscale-overview)
* [Azure CLI 监视自动缩放](https://docs.microsoft.com/cli/azure/monitor/autoscale?view=azure-cli-latest)