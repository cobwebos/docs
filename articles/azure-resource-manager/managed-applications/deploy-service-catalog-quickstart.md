---
title: 使用 Azure 门户部署服务目录应用程序
description: 向托管应用程序的使用者展示了如何通过 Azure 门户部署服务目录应用。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 3fa9709e096e908907772c940fc5e2f2895b7eb3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650781"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>通过 Azure 门户部署服务目录应用

在[前面的快速入门](publish-managed-app-definition-quickstart.md)中，你已发布了一个托管应用程序定义。 在本快速入门中，你将基于该定义创建一个服务目录应用。

## <a name="create-service-catalog-app"></a>创建服务目录应用

在 Azure 门户中，使用以下步骤：

1. 选择“创建资源”。

   ![创建资源](./media/deploy-service-catalog-quickstart/create-new.png)

1. 搜索“服务目录托管应用程序”并从可用选项中选择它。

   ![搜索服务目录应用程序](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. 你会看到托管应用程序服务的说明。 选择“创建”。

   ![选择“创建”](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. 门户中将显示你有权访问的托管应用程序定义。 从可用的定义中，选择你要部署的一个定义。 在本快速入门中，使用你在前面的快速入门中创建的“托管存储帐户”定义。 选择“创建”。

   ![选择要部署的定义](./media/deploy-service-catalog-quickstart/select-definition.png)

1. 提供 "**基本**信息" 选项卡的值。选择要将服务目录应用程序部署到的 Azure 订阅。 创建一个名为 **applicationGroup** 的新资源组。 为你的应用选择一个位置。 完成后，选择“确定”。

   ![为基本信息提供值](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. 为存储帐户名称提供一个前缀。 选择要创建的存储帐户的类型。 完成后，选择“确定”。

   ![为存储提供值](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. 查看摘要。 在验证成功后，选择“确定”以开始部署。

   ![查看摘要](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>查看结果

在服务目录应用完成部署后，你会得到两个新的资源组。 一个资源组用于存放服务目录应用。 另一个资源组用于存放服务目录应用的资源。

1. 查看名为 **applicationGroup** 的资源组来查看服务目录应用。

   ![查看应用程序](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. 查看名为 **applicationGroup{hash-characters}** 的资源组来查看服务目录应用的资源。

   ![查看资源](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建托管应用程序的定义文件，请参阅[创建并发布托管应用程序定义](publish-service-catalog-app.md)。
* 对于 Azure CLI，请参阅[使用 Azure CLI 部署服务目录应用](./scripts/managed-application-cli-sample-create-application.md)。
* 对于 PowerShell，请参阅[使用 PowerShell 部署服务目录应用](./scripts/managed-application-poweshell-sample-create-application.md)。