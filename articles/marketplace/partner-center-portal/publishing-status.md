---
title: 检查你的商业应用商店产品/服务的发布状态
description: 检查通过 Microsoft 合作伙伴中心的商业 Marketplace 发布产品/服务所需的验证、认证和预览步骤的状态。
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ec2a174c365e31520fe99ca0b505bbb62faad8c6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883403"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>检查你的商业应用商店产品/服务的发布状态

可以在合作伙伴中心中的[商业应用商店门户](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)的 "**产品/服务概述**" 选项卡上查看当前**发布状态**。

对于每个套餐，应会显示以下状态指示器之一。

| **Status**    | **说明**  |
| :---------- | :-------------------|
| **Draft** | 已创建产品/服务，但未发布该产品/服务。 |
| **发布正在进行** | 产品/计划正在执行发布过程的步骤。 |
| **需要注意** | Microsoft 或任何发布步骤在认证期间发现了严重问题。 |
| **预览** | 产品/服务已通过 Microsoft 认证，现在等待发布者最终验证。 选择 "上线" 以激活产品/服务。 |
| **Live** | 产品/服务在 marketplace 中推出，可供客户使用。 |
| **等待停止销售** | 发布者选择了 "停止销售" 产品/服务或计划，但该操作尚未完成。 |
| **在 marketplace 中不可用** | 已删除 marketplace 中以前发布的产品/计划。 |

## <a name="automated-validation"></a>自动验证

发布过程的第一步是一组自动验证。 每个验证步骤对应于在创建产品/服务时选择要启用的功能。 如果未启用该功能，则验证将跳到下一个发布步骤。 必须先完成每个验证检查，然后才能批准发布状态。

- **提供购买流程设置（< 10 分钟）**

在此步骤中，我们将确保你的产品/服务可以在通过 Azure 门户向客户购买时完成。 此步骤仅适用于通过 Microsoft 销售的产品/服务。

- **测试驱动器数据验证（约5分钟）**

在此步骤中，我们将验证产品/服务的 "测试驱动器技术配置" 部分中提供的数据。 测试驱动功能经过测试和批准。 此步骤仅适用于启用了测试驱动器的产品/服务。

- **测试驱动器设置（大约30分钟）**

在此步骤中，在上一步中验证测试驱动器的数据和功能后，我们将部署并复制你的测试驱动器的实例，以使其可供客户使用。  此步骤仅适用于启用了测试驱动器的产品/服务。

- **潜在客户管理验证和注册（< 15 分钟）**

在此步骤中，我们确认你的潜在客户管理系统可以根据产品/服务设置中提供的详细信息来接收客户领导。 此步骤仅适用于启用了潜在客户管理的产品/服务。

## <a name="certification"></a>认证

在发布之前，必须先认证合作伙伴中心的产品/服务。 已提交的产品/服务是严格的测试，一些自动和其他手册，包括对[Azure Marketplace 认证策略](https://docs.microsoft.com/legal/marketplace/general-policies)的检查。 必须将产品/服务提交标记为符合认证，然后才能继续执行发布流中的下一步。

### <a name="types-of-validation-that-take-place-during-certification"></a>认证期间发生的验证类型

提交的每个提议在认证过程中都包含三个验证级别。

- 发布者业务资格
- 内容验证
- 技术验证

#### <a name="publisher-business-eligibility"></a>发布者业务资格

每种产品/服务类型都会检查发布者必须满足的一组基本资格准则。 资格标准可能包括发布者的 MPN 状态、胜任度、资格级别等。

#### <a name="content-validation"></a>内容验证

在内容验证期间，会检查创建产品/服务时输入的信息的质量和相关性。 这些检查将检查 marketplace 的条目，以列出详细信息、定价、可用性、相关计划等。为了满足 Azure Marketplace 和/或 AppSource 列出的条件，我们将验证你的产品/服务是否包括：

- 准确描述产品/服务的标题;
- 编写好的说明，提供全面概述和价值陈述;
- 质量屏幕截图和随附的视频;与
- 此产品/服务如何利用 Microsoft 平台和工具的说明。

阅读[一般列表策略](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)，了解有关内容验证条件的详细信息。

#### <a name="technical-validation"></a>技术验证

在技术验证期间，产品/服务（包或二进制）会经历以下检查。
- 已扫描恶意软件
- 监视的网络调用
- 已分析包
- 全面扫描产品/服务的实际功能

在各种平台和版本中测试产品/服务，以确保其可靠性。

请查看本文档的技术配置部分中提供的特定配置详细信息。

### <a name="certification-failure-report"></a>认证失败报告

完成评审后，如果产品/服务已通过认证，则会将其移动到发布过程中的下一步。 如果产品/服务的任何清单、技术或策略检查失败，或者如果你不能提交该类型的产品/服务，则会生成一个证书故障报告，并通过电子邮件发送给你。

此报告包含任何失败策略的说明以及查看注释。 查看此电子邮件报告，解决任何问题，根据需要更新产品/服务，并使用合作伙伴中心的 "[商用 Marketplace 门户](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)" 重新提交产品/服务。 （你可以根据需要重新提交产品/服务，直到通过认证）。

## <a name="preview-creation"></a>预览创建

在**预览创建**步骤中，我们会创建一个版本的产品/服务，该版本仅可供你在产品/服务的预览部分中指定的受众访问。

>[!Note]
> 不要使用此步骤向组织外部的人员显示产品/服务。 改为使用 "**专用产品/服务**" 选项。 此时，您的产品/服务尚未经过全面测试和验证，且未准备好进行外部分发。

## <a name="publisher-approval"></a>出版商审批

在此步骤中，你将通过电子邮件发送请求，以便在最终发布步骤之前查看和批准产品/服务预览版。

如果你已选择通过 Microsoft 出售你的产品/服务，你将能够测试产品/服务的购买和部署，以确保它在此预览审批阶段满足你的要求。 你的产品/服务在公共 marketplace 中将不可用。 测试并批准此预览版后，你将需要选择 "产品 **/** [**服务概述**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)" 仪表板上的 "开始"。

如果要在此预览阶段对产品/服务进行更改，则可以编辑并重新提交以发布新预览。 有关更多更改的详细信息，请参阅[更新现有 marketplace 产品/服务](#update-existing-marketplace-offers)一文。

如果产品/服务已处于活动状态且可供 marketplace 中的公共用户使用，则在[**产品/服务概述**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)仪表板上选择 "**开始**使用" 之前，你所做的任何更新都不会生效。

### <a name="publish-offer-to-the-public"></a>将产品/服务发布到公共

登录到合作伙伴中心，并访问产品/服务。 你将被重定向到 "**产品概述**" 页。 在此页的顶部，你将看到 **"上线" 选项。** 选择 "上线" **，** 确认后，产品/服务将开始发布到公共。 产品/服务处于活动时，你将收到电子邮件通知。

## <a name="publish"></a>发布

现在，你已**选择使用产品**/服务，使其在 marketplace 中可用，接下来将介绍一系列最终验证检查，以确保实时产品/服务的配置与预览版的预览版本一样。

- **提供购买流程设置（> 10 分钟）**

在此步骤中，我们将确保你的产品/服务可以在通过 Azure 门户向客户购买时完成。 此步骤仅适用于通过 Microsoft 销售的产品/服务。

- **测试驱动器数据验证（约5分钟）**

在此步骤中，我们将验证产品/服务的 "测试驱动器技术配置" 部分中提供的数据。 测试驱动功能经过测试和批准。 此步骤仅适用于启用了测试驱动器的产品/服务。

- **测试驱动器设置（大约30分钟）**

在此步骤中，我们将部署并复制你的测试驱动器的实例，以使其可供客户使用。  此步骤仅适用于启用了测试驱动器的产品/服务。

- **潜在客户管理验证和注册（> 15 分钟）**

在此步骤中，我们确认你的潜在客户管理系统可以根据产品/服务设置中提供的详细信息来接收客户领导。 此步骤仅适用于启用了潜在客户管理的产品/服务。

- **最终发布（> 30 分钟）**

在此步骤中，我们将确保产品/服务在 marketplace 中公开发布。

## <a name="update-existing-marketplace-offers"></a>更新现有 marketplace 产品

如果要对已发布的产品/服务进行更改，则需要先更新现有产品/服务，然后再次发布。

## <a name="next-steps"></a>后续步骤

- [更新商业应用商店中的现有产品/服务](./update-existing-offer.md)
