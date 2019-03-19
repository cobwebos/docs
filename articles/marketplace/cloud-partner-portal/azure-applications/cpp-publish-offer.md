---
title: 发布 Azure 应用程序套餐 - Azure 市场 | Microsoft Docs
description: 介绍在 Azure 市场上发布 Azure 应用程序产品/服务的过程和步骤。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: pbutlerm
ms.openlocfilehash: fdca47877d4cb2192eef0a26448cd21e8afe4b77
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217931"
---
# <a name="publish-azure-application-offer"></a>发布 Azure 应用程序产品/服务

通过在“新建套餐”页上提供信息创建套餐后，可以发布该套餐。 选择“发布”启动发布过程。

下图显示了发布套餐以使其“上线”的过程的主要步骤。

![产品/服务发布步骤](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>发布步骤的详细说明

下表列出并描述了每个发布步骤，并提供了完成该步骤预计所需的时间。  预计时间以“天”为单位，以工作日定义，不包括周末和节假日。

|  **发布步骤**           | **时间**    | **说明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 验证先决条件         | 小于 15 分钟    | 验证套餐信息和套餐设置。                        |
| 验证影响的收入设置 | 小于 15 分钟  | 已选中产品/服务的 Azure 资源使用情况属性。             |
| 认证                  | 小于 1 天     | Azure 认证团队对套餐进行分析。 针对病毒、恶意软件、安全合规性和安全问题对产品/服务进行扫描。 检查产品/服务，以了解它是否满足所有资格条件。 有关详细信息，请参阅[先决条件](./cpp-prerequisites.md)。 如果发现了问题，则提供反馈。 |
| 体验版验证          | 小于 2 小时   | （可选）如果存在体验版，Microsoft 将验证是否可以部署和复制该体验版。  |
| 打包以及潜在顾客开发注册 | 小于 1 小时  | 打包套餐的技术资产供客户使用，配置并部署潜在顾客系统。 |
|  发布者签核             |  manual    | 最终发布者在套餐上线之前进行复查和确认。 套餐现在可供预览。  可以在所选订阅（在套餐信息步骤中）中部署套餐，以验证它是否符合所有要求。  验证套餐后，选择“上线”，使套餐可以转移到下一步骤。 |
| Microsoft 评审                | 7 - 14 天 | Microsoft 对 Azure 应用程序进行整体评审，如果发现问题，将通过电子邮件告诉你。  此步骤所需的时间取决于应用程序的复杂性、发现的问题，以及你对这些问题的响应速度。  |
| 实时                           | 小于 1 天 | 发布套餐，将其复制到指定的区域，并使其可供公众使用。 |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

可以在云合作伙伴门户上套餐的“状态”选项卡中监视发布过程。

![Azure 应用套餐的“状态”选项卡](./media/offer-status-tab.png)

完成发布过程后，套餐将在 [Microsoft Azure 市场应用程序类别](https://azuremarketplace.microsoft.com/marketplace/apps/)中列出。

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴通道参加现已推出。  请参阅[云解决方案提供商](../../cloud-solution-providers.md)营销通过 Microsoft CSP 产品/服务的详细信息的合作伙伴渠道。

## <a name="errors-and-review-feedback"></a>错误和评审反馈

除了显示产品/服务的发布状态以外，“状态”选项卡还显示从遇到问题的任何发布步骤返回的错误消息和反馈。  如果问题严重，则发布会被取消。  必须更正所报告的问题，然后重新发布产品/服务。  因为 **Microsoft 评审**步骤表示对你的产品/服务及其相关技术资产（尤其是 Azure 资源管理器模板）的广泛评审，所以问题通常呈现为拉取请求 (PR) 链接。  有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](./cpp-handling-review-feedback.md)。


## <a name="next-steps"></a>后续步骤

如果在一个或多个发布步骤中遇到错误，则必须更正错误并重新发布产品/服务。  如果在 **Microsoft 评审**步骤中遇到严重问题，则必须访问 Microsoft 评审团队的 Azure DevOps 存储库来[处理评审反馈](./cpp-handling-review-feedback.md)。

成功发布 Azure 应用后，可以[更新现有产品/服务](./cpp-update-existing-offer.md)来反映业务或技术要求的变化。 
