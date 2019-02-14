---
title: 发布 Power BI 应用产品/服务 - Azure 市场 | Microsoft Docs
description: 在 Microsoft AppSource 市场上发布 Power BI 应用产品/服务。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665708"
---
# <a name="publish-power-bi-app-offer"></a>发布 Power BI 应用产品/服务

在门户中定义产品/服务并创建关联的技术资产后，最后一个步骤是提交产品/服务进行发布。  若要开始此过程，请在“新产品/服务”窗口中单击垂直菜单上的“发布”按钮。  有关详细信息，请参阅[发布 Azure 市场和 AppSource 产品/服务](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>发布步骤

下图描述了进行“推出”的发布过程的主要步骤。

![Power BI 应用的发布过程步骤](./media/publishing-process-steps.png)

下表介绍了这些步骤并提供了完成它们所需的最大估计时间：

|   发布步骤            |   时间     |   说明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 验证先决条件       | 15 分钟     | 验证套餐信息和套餐设置。                            |
| 认证                | 1-7 天   | Power BI 认证团队会分析产品/服务。 我们会通过提供的安装 URL 安装应用，从而通过手动验证测试来运行你的 Power BI 应用。 在应用认证过程中执行主要验证；请参阅下文。         |
| 打包                    | \< 1 小时  | 产品/服务的技术资产会进行打包，以供客户使用。                        |
| 潜在顾客开发注册 | \< 1 小时  | 配置和部署潜在顾客系统。                                      |
| 发布者签收            | \-         | 最终发布者在套餐上线之前进行复查和确认。 你现在还具有用于预览产品/服务的链接。 对预览版的样子感到满意后，在“状态”选项卡上单击“推出”按钮。此操作会向实施团队发送请求以在 AppSource 上列出你的应用。    |
| 实时                         | \< 3 小时 | 你的产品/服务现已在 AppSource 上公开列出（“推出”），客户将能够查看并在其 Power BI 订阅中部署你的应用。 你还会收到确认电子邮件。 可以随时单击“所有产品/服务”选项卡，查看在右侧栏中列出的所有产品/服务的状态。 单击“状态”即可详细查看产品/服务的发布流状态。 |
|   |   |

留出最多八天来完成此过程。 完成这些发布步骤后，你的 Power BI 应用产品/服务将在 [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 应用部分中列出。


### <a name="app-certification-process"></a>应用认证过程

Microsoft 实施团队会使用以下过程验证你的 Power BI 产品/服务提交：

1. 评审法律文档和帮助链接。
2. 验证支持联系人信息。
3. 使用安装程序 URL 验证是否可正确安装。 
4. 扫描应用中是否存在恶意软件和其他恶意内容。 
5. 执行验证以查看显示的内容是否应用说明相符。
6. 与应用相关的操作在 Power BI 中按预期方式工作：使用示例数据打开报表和仪表板、连接到自定义数据源、刷新等。

如果发现任何问题，则认证团队会提供反馈。  有关 Power BI 应用要求的详细信息，请参阅 [Power BI 应用文档](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>后续步骤

建议在 [AppSource 市场](https://appsource.microsoft.com)中定期监视你的应用。  此外，应使用[云合作伙伴门户](https://cloudpartner.azure.com/#insights)的 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 功能来提供有关市场客户和使用情况的见解。  还可以执行某些[产品/服务更新](./cpp-update-existing-offer.md)。
