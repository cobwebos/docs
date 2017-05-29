---
title: "Azure 开发测试实验室中你自己的实验室的缩放配额和限制 | Microsoft Docs"
description: "了解如何在 Azure 开发测试实验室中缩放实验室"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: f11ed42b474e4f208eac92689bfa33fb188d15a1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>开发测试实验室中的缩放配额和限制
在开发测试实验室中工作时，你可能会注意到，某些 Azure 资源存在某些默认的限制，这可能会影响开发测试实验室服务。 这些限制称为“配额”。

> [!NOTE]
> 开发测试实验室服务不实施任何配额。 你可能遇到的任何配额都是整个 Azure 订阅的默认约束。

在达到配额之前，可以随意使用每个 Azure 资源。 每个订阅都有单独的配额，使用情况按订阅跟踪。

例如，每个订阅的默认配额为 20 个核心。 因此，如果要在实验室中创建 VM，每个 VM 四个核心，则只能创建五个 VM。 

[Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)列出了某些最常用于 Azure 资源的配额。 最常用于实验室且带配额的资源包括：VM 核心、公共 IP 地址、网络接口、托管磁盘、RBAC 角色分配，以及 ExpressRoute 线路。

## <a name="view-your-usage-and-quotas"></a>查看使用情况和配额
以下步骤演示了如何查看订阅中特定 Azure 资源的当前配额，以及如何查看每个配额已使用的百分比。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 选择“更多服务”，然后从列表中选择“计费”。
1. 在“计费”边栏选项卡中，选择一个订阅。
4. 选择“使用情况 + 配额”。

   ![“使用情况和配额”按钮](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   此时会显示“使用情况 + 配额”边栏选项卡，列出该订阅中的可用资源以及每个资源已使用配额的百分比。

   ![配额和使用情况](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>请求在订阅中提供更多资源
如果达到配额限制，可以将订阅中资源的默认限制提高到最大限制，详见 [Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)。

以下步骤演示如何通过 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)请求提高配额。

1. 依次选择“更多服务”、“计费”、“使用情况 + 配额”。
1. 在“使用情况 + 配额”边栏选项卡中，选择“请求提高配额”按钮。

   ![“请求提高配额”按钮](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. 若要完成并提交请求，请在“新建支持请求”表单的所有三个选项卡上填写必填信息。

   ![“请求提高配额”表单](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Understanding Azure Limits and Increases](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)（了解 Azure 限制和配额提高）详细介绍了如何通过联系 Azure 支持部门来请求提高配额。



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>后续步骤
* 浏览[开发测试实验室 Azure Resource Manager 快速入门模板库](https://github.com/Azure/azure-devtestlab/tree/master/Samples)。

