---
title: 发布 Power BI 应用服务 |Azure Marketplace
description: 在 Microsoft AppSource marketplace 上发布 Power BI 应用服务。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pabutler
ms.openlocfilehash: 324be960bd2d22623763ca3e24b99be92ff04174
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826039"
---
# <a name="publish-a-power-bi-app-offer"></a>发布 Power BI 应用服务

最后一步，在云合作伙伴门户中定义产品/服务并创建关联的技术资产后，将提交产品/服务进行发布。 若要启动此过程，请在 "**新建产品/服务**" 窗口的左窗格中，选择 "**发布**"。 有关详细信息，请参阅[发布 Azure 市场和 AppSource 产品/服务](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>发布步骤

下面是发布过程的主要步骤：

![发布 Power BI 应用服务的过程步骤](./media/publishing-process-steps.png)

下表描述了每个步骤，并提供了其估计完成时间：

|   发布步骤            |   时间     |   说明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 验证先决条件       | 15 分钟     | 验证产品/服务信息和产品/服务设置。                            |
| 认证                | 1-7 天   | Power BI 认证团队会分析你的产品/服务。 团队通过通过提供的安装 URL 安装应用，通过手动验证测试运行 Power BI 应用。 主要验证作为应用程序认证过程的一部分执行（本文档稍后将对此进行介绍）。         |
| 打包                    | \< 1 小时  | 产品/服务的技术资产已打包供客户使用。                        |
| 潜在客户生成注册 | \< 1 小时  | 配置和部署潜在顾客系统。                                      |
| 发布者签收            | \-         | 在产品/服务推出之前，请先完成最后的评审和确认。 现在你还会获得一个用于预览你的产品/服务的链接。 对预览外观感到满意后，选择 "**状态**" 选项卡上的 "**开始**"。这会向加入团队发送请求，以在 AppSource 上列出你的应用。    |
| 实时                         | \< 3 小时 | 你的产品/服务现已公开在 AppSource 上（"实时"），客户可以查看你的应用并将其部署到 Power BI 订阅中。 你还将收到一封确认电子邮件。 在 "**所有产品/服务**" 选项卡的右侧列中，可以看到所有产品/服务的状态。 在 "**状态**" 选项卡上，可以看到产品/服务的详细发布流程状态。 |
|   |   |

完成此过程最多允许8天。 完成这些发布步骤后，你的 Power BI 应用产品/服务将在[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI apps 部分列出。


### <a name="app-certification-process"></a>应用认证过程

Microsoft 载入团队将使用此过程来验证 Power BI 应用提议提交：

1. 查看法律文档和帮助链接。
2. 验证支持联系人信息。
3. 使用安装程序 URL 来验证安装是否正确。
4. 扫描应用程序中是否有恶意软件和其他恶意内容。
5. 验证显示的内容是否与应用程序的说明相匹配。
6. 验证应用相关操作在 Power BI 中是否按预期方式工作。 团队打开带有示例数据的报表和仪表板，连接到自定义数据源，刷新数据等。

如果发现任何问题，则认证团队会提供反馈。  有关 Power BI 应用要求的详细信息，请参阅[Power BI 应用文档](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>后续步骤

建议你定期监视[AppSource marketplace](https://appsource.microsoft.com)中的应用。  还应使用[云合作伙伴门户](https://cloudpartner.azure.com/#insights)的[卖方 insights](../../cloud-partner-portal-orig/si-getting-started.md)功能来获取有关 marketplace 客户和应用使用情况的见解。 最后，你可以[更新你的产品/服务](./cpp-update-existing-offer.md)。
