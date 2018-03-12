---
title: "使用情况报告基础结构为云服务提供商 Azure 堆栈 |Microsoft 文档"
description: "Azure 堆栈包括跟踪使用情况，因为它发生，并将其转发到 Azure 所需的基础结构。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 4ac808e0e85b1daeb54a3f2fd7bec0a7c10aa13e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>使用情况报告为云服务提供商的基础结构

Azure 堆栈包括跟踪使用情况，因为它发生，并将其转发到 Azure 所需的基础结构。 在 Azure 中，Azure 商务处理使用情况数据和收费的使用情况与相应的 Azure 订阅，将发生在全局 Azure 云的使用情况的方式相同。

你应注意的某些概念是全球 Azure 和 Azure 堆栈之间一致。 Azure 堆栈具有本地订阅，满足类似角色到 Azure 订阅。 本地订阅本地才有效。 使用情况转发到 Azure 时，本地订阅将映射到 Azure 订阅。

Azure 堆栈具有本地用量计量表。 本地使用情况将映射到在 Azure 商务中使用的计量。 但是的计数 Id 是不同的。 本地比一个 Microsoft 使用计费有可用的多个米为单位。

有一些差异服务如何在 Azure 堆栈和 Azure 定价的信息。 例如，在 Azure 堆栈 Vm 的费用仅基于 vcore/小时，对于所有 VM 系列，与 Azure 不同相同的费率。 原因是，在全球 Azure 中不同的价格反映不同的硬件。 在 Azure 堆栈客户提供硬件，因此没有理由要收取为不同的 VM 类不同的速率。

你可以了解有关的 Azure 服务一样，在商务和在合作伙伴中心中，其价格中相同的方式使用的 Azure 堆栈计量器：

1. 在合作伙伴中心，转到**仪表板菜单** > **价格与优惠**。
2. 下**基于使用情况的服务**，选择**当前**。
3. 打开**Azure 全局 CSP 价目表中**电子表格。
4. 筛选**区域 = Azure 堆栈**。

## <a name="usage-and-billing-error-codes"></a>使用情况和计费错误代码

添加对注册租户时，可能遇到以下错误消息。

| 错误                           | 详细信息                                                                                                                                                                                                                                                                                                                           | 注释                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | 找不到提供的注册。 请确保正确提供以下信息：<br>1.订阅标识符 (提供值：_订阅标识符_)，<br>2.资源组 (提供值：_资源组_)，<br>3.注册名称 (提供值：_注册名称_)。                             | 指向在初始注册的信息不正确时，通常会发生此错误。 如果你需要验证的资源组和你的注册名称，则你可以通过列出的所有资源而在 Azure 门户中，进行查找。 如果发现多个注册资源，请看一看 CloudDeploymentID 在属性中，并选择其 CloudDeploymentID 与匹配你的云的注册。 若要查找 CloudDeploymentID，你可以在 Azure 堆栈上使用此 PowerShell:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | 提供_客户订阅标识符_和_注册名称_订阅标识符不归相同的 Microsoft 云服务提供商。 检查客户的订阅标识符正确。 如果问题持续出现，请联系支持人员。 | 客户订阅是 CSP 订阅，但它汇总到的 CSP 合作伙伴不同于向其在初始注册中使用的订阅汇总，会出现此错误。 若要防止将导致计费不负责使用 Azure 堆栈的 CSP 合作伙伴的情况下，会进行此检查。                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | _客户订阅标识符_无效。 请确保提供有效的 Azure 订阅。                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _客户订阅标识符_找不到 _registration 名称下。 请确保正在使用有效的 Azure 订阅和订阅标识符已添加到使用 PUT 操作的注册。                                                   | 尝试验证租户已添加到订阅，并客户订阅找不到要与注册相关联时，将出现此错误。 客户尚未添加到注册，或订阅 ID 编写不正确。                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | 提供_注册名称_未获批准以使用多租户。 发送电子邮件至azstCSP@microsoft.com，包括你注册的名称、 资源组和在注册中使用的订阅标识符。                                                                                    | 注册需要之前必须批准针对多租户 microsoft 可以开始将租户添加到它。 有关更多说明本文档中的部分注册租户，请参阅。                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | 为断开连接的客户不支持客户订阅操作。 若要使用此功能，重新注册与付费作为你使用许可。                                                                                                                                                                    | 要添加租户注册是容量注册，即在创建注册后，BillingModel 容量已使用的参数。 只需付费使用注册允许添加租户。 你需要使用参数 BillingModel PayAsYouUse 重新注册。                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | 提供_客户订阅标识符_不是有效的 CSP 订阅。 请确保提供有效的 Azure 订阅。                                                                                                                                                        | 这通常很可能是由于正在输入正确的客户订阅。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | 上游服务器之一返回了意外的错误。 请稍后重试。 如果问题仍然存在，与客户支持联系。                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>有关帐单和使用情况的术语

以下术语和概念用于使用情况和计费 Azure 堆栈中：

| 术语 | 定义 |
| --- | --- |
| 直销 CSP 合作伙伴 | 直接的云解决方案提供程序 (CSP) 合作伙伴直接从 Microsoft Azure 和 Azure 堆栈的使用情况，以及帐单客户直接接收发票。 |
| 间接 CSP | 间接经销商工作使用间接提供程序 （也称为分发服务器）。 经销商招聘最终客户;间接的提供程序包含与 Microsoft 付费的关系，管理客户计费，并提供其他服务，如产品支持。 |
| 最终用户 | 最终客户是企业和政府机构拥有应用程序和 Azure 堆栈运行其他工作负荷。 |

## <a name="next-steps"></a>后续步骤

 - 若要了解有关 CSP 计划的更多信息，请参阅[云解决方案提供商计划](https://partnercenter.microsoft.com/en-us/partner/programs)。
 - 若要了解有关如何从 Azure 堆栈中检索资源使用情况信息的详细信息，请参阅[使用情况和 Azure 堆栈中的计费](/azure-stack-billing-and-chargeback.md)。
