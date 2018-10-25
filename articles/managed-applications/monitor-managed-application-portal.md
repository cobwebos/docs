---
title: 使用 Azure 门户监视托管应用程序 | Microsoft Docs
description: 显示如何使用 Azure 门户监视托管应用程序的可用性和警报。
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805684"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>监视部署的托管应用程序实例

将托管应用程序部署到 Azure 订阅后，可能需要检查应用程序的状态。 本文介绍 Azure 门户中用于检查状态的选项。 在托管应用程序中，可以监视资源的可用性。 还可以设置和查看警报。

## <a name="view-resource-health"></a>查看资源运行状况

1. 选择托管应用程序实例。

   ![创建托管应用程序](./media/monitor-managed-application-portal/select-managed-application.png)

1. 选择“资源运行状况”。

   ![选择资源运行状况](./media/monitor-managed-application-portal/select-resource-health.png)

1. 在托管应用程序中查看资源的可用性。

   ![查看资源运行状况](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>查看警报

1. 选择“**警报**”。

   ![选择警报](./media/monitor-managed-application-portal/select-alerts.png)

1. 如果已配置警报规则，则会看到有关已引发警报的信息。

   ![查看警报](./media/monitor-managed-application-portal/view-alerts.png)

1. 若要添加警报规则，请选择“+ 新建警报规则”。

   ![创建警报](./media/monitor-managed-application-portal/create-new-alert.png)

可以针对托管应用程序实例或托管应用程序中的资源创建警报。 有关创建警报的信息，请参阅 [Microsoft Azure 中的警报概述](../monitoring-and-diagnostics/monitoring-overview-alerts.md)。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
* 若要部署托管应用程序，请参阅[通过 Azure 门户部署服务目录应用](deploy-service-catalog-quickstart.md)。