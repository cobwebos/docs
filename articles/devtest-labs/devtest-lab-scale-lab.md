---
title: Azure 开发测试实验室中自己的实验室的缩放配额和限制 | Microsoft Docs
description: 本文介绍如何在 Azure 开发测试实验室中缩放实验室。 查看使用量配额和限制，并请求增加。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2166eaab073e2abc24ca24494ae13eb876db1fcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533948"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>开发测试实验室中的缩放配额和限制
在开发测试实验室中工作时，可能会注意到，某些 Azure 资源存在某些默认的限制，这可能会影响开发测试实验室服务。 这些限制称为“配额”****。

> [!NOTE]
> 开发测试实验室服务不实施任何配额。 可能遇到的任何配额都是整个 Azure 订阅的默认约束。

在达到配额之前，可以随意使用每个 Azure 资源。 每个订阅都有单独的配额，使用情况按订阅跟踪。

例如，每个订阅的默认配额为 20 个核心。 因此，如果要在实验室中创建 VM，每个 VM 四个核心，则只能创建五个 VM。

[Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)列出了某些最常用于 Azure 资源的配额。 实验室最常使用的资源，其中你可能会遇到配额，包括 VM 核心、公共 IP 地址、网络接口、托管磁盘、Azure 角色分配和 ExpressRoute 线路。

## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额
以下步骤演示了如何查看订阅中特定 Azure 资源的当前配额，以及如何查看每个配额已使用的百分比。

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“更多服务”****，并从列表中选择“计费”****。
1. 在“计费”边栏选项卡中，选择一个订阅。
4. 选择 " **使用情况 + 配额**"。

   ![“使用情况和配额”按钮](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   此时会显示“使用情况 + 配额”边栏选项卡，列出该订阅中的可用资源以及每个资源已使用配额的百分比。

   ![配额和使用情况](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>请求在订阅中提供更多资源
如果达到配额限制，可以将订阅中资源的默认限制提高到最大限制，详见 [Azure 订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

以下步骤演示如何通过 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)请求提高配额。

1. 依次选择“更多服务”****、“计费”****、“使用情况 + 配额”。****
1. 在“使用情况 + 配额”边栏选项卡中，选择“请求提高配额”按钮。****

   ![“请求提高配额”按钮](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. 若要完成并提交请求，请在“新建支持请求”表单的所有三个选项卡上填写必填信息。****

   ![“请求提高配额”表单](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)（了解 Azure 限制和配额提高）详细介绍了如何通过联系 Azure 支持部门来请求提高配额。



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>后续步骤
* 探索 [开发测试 Labs Azure 资源管理器快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。
