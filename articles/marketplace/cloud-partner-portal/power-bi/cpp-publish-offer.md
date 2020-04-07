---
title: 发布电源 BI 应用产品 /服务 |Azure 应用商店
description: 在 Microsoft AppSource 市场上发布 Power BI 应用产品/服务。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d52270e623a713d7188ac5ff907ebb82ada13283
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745609"
---
# <a name="publish-a-power-bi-app-offer"></a>发布 Power BI 应用产品/服务

>[!Important]
>从 2020 年 3 月 30 日开始，我们将开始将 Power BI 应用优惠的管理转移到合作伙伴中心。 迁移后，您将在合作伙伴中心创建和管理您的优惠。 按照[Power BI 应用创建概述](https://aka.ms/AzureCreatePBIServiceApp)中的说明进行操作，以管理迁移的优惠。

在云合作伙伴门户中定义产品/服务并创建关联的技术资产后，最后一步是提交发布产品/服务。 要启动此过程，请在 **"新优惠**"窗口的左侧窗格中，选择 **"发布**"。 有关详细信息，请参阅[发布 Azure 市场和 AppSource 产品/服务](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>发布步骤

以下是发布过程中的主要步骤：

![发布 Power BI 应用产品/服务的过程步骤](./media/publishing-process-steps.png)

下表描述了每个步骤，并提供其估计完成时间：

|   发布步骤            |   时间     |   说明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 验证先决条件       | 15 分钟     | 验证套餐信息和套餐设置。                            |
| 认证                | 1-7 天   | Power BI 认证团队分析您的产品/服务。 团队通过手动验证测试运行您的 Power BI 应用，通过提供的安装 URL 安装应用。 主验证作为应用认证过程的一部分执行（本文档后面所述）。         |
| 打包                    | \< 1 小时  | 产品/服务的技术资产打包供客户使用。                        |
| 铅生成注册 | \< 1 小时  | 配置和部署潜在顾客系统。                                      |
| 发布者签收            | \-         | 在优惠生效之前，您完成最终审核和确认。 现在，您还将有一个链接来预览您的产品/ 对预览的外观感到满意后，请在"**状态**"选项卡上选择 **"上线**"。这将向载入团队发送请求，以在 AppSource 上列出您的应用。    |
| 实时                         | \< 3 小时 | 您的产品/服务现已在 AppSource 上公开列出（"实时"），客户可以查看您的应用并将其部署在其 Power BI 订阅中。 您还将收到确认电子邮件。 在"**所有优惠"** 选项卡的右列中，您可以看到所有优惠的状态。 在"**状态"** 选项卡上，您可以看到产品/服务的详细发布流状态。 |
|   |   |

此过程最多允许八天。 完成这些发布步骤后，您的 Power BI 应用产品/服务将列在[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 应用部分。


### <a name="app-certification-process"></a>应用认证过程

Microsoft 载入团队使用此过程验证您的 Power BI 应用产品/服务提交：

1. 查看法律文档和帮助链接。
2. 验证支持联系信息。
3. 使用安装程序 URL 验证正确的安装。
4. 扫描应用以发现恶意软件和其他恶意内容。
5. 验证显示的内容是否与应用的说明匹配。
6. 验证与应用相关的操作在 Power BI 中是否按预期工作。 团队打开包含示例数据的报告和仪表板、连接到自定义数据源、刷新数据等。

如果发现任何问题，则认证团队会提供反馈。  有关 Power BI 应用要求的详细信息，请参阅[Power BI 应用文档](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>后续步骤

我们建议您定期在[AppSource 市场](https://appsource.microsoft.com)中监视你的应用程序。  您还应使用[云合作伙伴门户](https://cloudpartner.azure.com/#insights)的[卖家见解](../../cloud-partner-portal-orig/si-getting-started.md)功能，获取有关市场客户和应用使用情况的见解。 最后，您可以[更新您的优惠](./cpp-update-existing-offer.md)。
