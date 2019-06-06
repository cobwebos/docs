---
title: 检查商业 Marketplace 产品/服务的发布状态
description: 在 Microsoft 合作伙伴中心发布通过商业 Marketplace 产品/服务所需的验证、 认证和预览步骤的状态的检查。
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474522"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>检查商业 Marketplace 产品/服务的发布状态

您可以查看您的当前**发布状态**上**产品/服务概述**选项卡[商业 Marketplace 门户](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)在合作伙伴中心。

对于每个套餐，应会显示以下状态指示器之一。

| **Status**    | **说明**  |
| :---------- | :-------------------|
| **Draft** | 已创建产品/服务，但它不要发布。 |
| **发布正在进行** | 产品/服务/计划正在其完成的步骤的发布过程的方式。 |
| **需要注意** | 由 Microsoft 或任何发布的步骤在认证期间发现的严重问题。 |
| **预览** | 产品/服务由 Microsoft、 认证和现在等待由发布者进行最终验证。 选择转活动状态，以使产品/服务实时。 |
| **Live** | 产品/服务在 marketplace 中处于活动和可见且可获取的客户。 |
| **挂起停止销售** | 发布服务器上的产品/服务或计划中，选择"停止销售"但尚未完成该操作。 |
| **在 marketplace 中不可用** | 在 marketplace 中的以前发布的产品/服务/计划已删除。 |

## <a name="automated-validation"></a>自动化的验证

在发布过程中的第一步是一组的自动验证。 每个验证步骤对应于你选择在创建产品/服务中启用的功能。 如果未启用该功能，验证向前跳到下一步的发布步骤。 批准发布的状态之前，必须完成每个验证检查。

- **产品/服务购买流安装程序 (< 10 分钟)**

在此步骤中，我们确保由客户通过 Azure 门户购买时，可以实现产品/服务。 此步骤才适用于通过 Microsoft 销售的产品/服务。

- **测试驱动器数据验证 （约 5 分钟）**

在此步骤中，我们验证体验版的产品/服务的技术配置节中提供的数据。 测试驱动器功能测试和批准。 与测试驱动器启用了此步骤才适用的产品/服务。

- **测试驱动器预配 （约 30 分钟）**

在此步骤中之后验证数据和功能的体验版在上一步骤中，, 我们将部署和复制体验版的实例，以便它们可供客户使用。  与测试驱动器启用了此步骤才适用的产品/服务。

- **会导致管理验证和注册 (< 15 分钟)**

在此步骤中，我们确认你的潜在客户管理系统可以接收基于提供的产品/服务设置的详细信息的潜在客户。 与潜在客户管理启用了此步骤才适用的产品/服务。

## <a name="certification"></a>认证

在发布之前必须经过认证产品/服务提交到合作伙伴中心中的商业应用商店。 提交产品/服务进行严格测试，某些自动化和手动的包括针对检查其他[Azure Marketplace 认证策略](https://docs.microsoft.com/legal/marketplace/general-policies)。 产品/服务提交必须标记为符合认证条件之前它们将继续到下一步中发布的流。

### <a name="types-of-validation-that-take-place-during-certification"></a>认证期间进行的验证类型

有三个级别的验证包含在提交每个产品/服务的认证过程。

- 发布服务器业务资格
- 内容验证
- 技术验证

#### <a name="publisher-business-eligibility"></a>发布服务器业务资格

每个产品/服务类型检查一的组基资格发布服务器必须满足的条件。 资格条件可能包括发布者的 MPN 状态、 保持竞争力、 成员资格级别等。

#### <a name="content-validation"></a>内容验证

在内容验证过程中创建产品/服务时输入的信息来检查质量和相关性。 这些检查将查看你的 marketplace 列表的详细信息，定价、 可用性、 关联的计划等的条目。为了满足 Azure Marketplace 和/或 AppSource 列出条件，我们将验证产品/服务包括：

- 准确描述产品/服务; 一个标题
- 编写良好的全面概述和价值主张; 提供的说明
- 质量屏幕截图和随附的视频;和
- 产品/服务如何利用 Microsoft 平台和工具的说明。

通过阅读了解有关内容验证条件的更多信息[常规列表策略](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies)。

#### <a name="technical-validation"></a>技术验证

技术在验证期间，产品/服务 （包或二进制文件） 将进行以下检查。
- 恶意软件扫描
- 监视的网络调用
- 分析包
- 产品/服务的实际功能的全面扫描

产品/服务以确保它是可靠测试跨各种平台和版本。

查看本文档的技术配置节产品/服务所需的特定配置详细信息。

### <a name="certification-failure-report"></a>认证失败报告

完成后查看，如果产品/服务已通过认证然后它按顺序进入发布过程的下一步。 如果产品/服务已失败的任何列表、 技术、 或策略检查，或者你没有资格提交该类型的产品/服务，认证失败报告生成，并通过电子邮件发送给你。

此报表包含失败，评审说明以及任何策略的说明。 查看此电子邮件报告、 解决任何问题，对产品/服务进行更新，其中有需要并重新提交产品/服务使用[商业 Marketplace 门户](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)在合作伙伴中心。 （您可以重新提交无数次，直到传递证书所需的产品/服务）。

## <a name="preview-creation"></a>预览创建

期间**预览创建**步骤中，我们创建一个版本的产品/服务产品/服务的预览部分中指定的受众可以访问。

## <a name="publisher-approval"></a>发布者批准

在此步骤中，您将其发送电子邮件与请求审阅并批准产品/服务预览版之前发布的最后一步。

如果选择了销售通过 Microsoft 产品/服务，你将能够测试采集和产品/服务以确保其满足您的要求，在此预览版审批阶段的部署。 产品/服务将尚不可用公共 marketplace 中。 一旦您测试和批准此预览版，你将需要选择**Go-live**上[**产品/服务概述**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)仪表板。

如果你想要在此预览阶段的产品/服务进行更改，可以编辑并重新提交要发布新的预览。 请参阅文章[现有的 marketplace 产品/服务的更新](#update-existing-marketplace-offers)有关更多更改的详细信息。

如果产品/服务已经实时和在 marketplace 中公开提供的直到您选择，所做的任何更新不会继续实时**上线**上[**产品/服务概述**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)仪表板。

### <a name="publish-offer-to-the-public"></a>向公众发布产品/服务

登录到合作伙伴中心和访问产品/服务。 你将重定向到**产品/服务概述**页。 在此页顶部，您将看到一个选项**投入**。 选择**进入正常运行状态，** 并确认后，，产品/服务将启动获取发布给公众。 实时产品/服务时，您将收到电子邮件通知。

## <a name="publish"></a>发布

既然您已经选择**投入**产品/服务，使其在 marketplace 中有一系列将逐步完成以确保实时产品/服务已配置就像在预览的最终验证检查产品/服务的版本。

- **产品/服务购买流量设置 (> 10 分钟)**

在此步骤中，我们确保由客户通过 Azure 门户购买时，可以实现产品/服务。 此步骤才适用于通过 Microsoft 销售的产品/服务。

- **测试驱动器数据验证 （约 5 分钟）**

在此步骤中，我们验证体验版的产品/服务的技术配置节中提供的数据。 测试驱动器功能测试和批准。 与测试驱动器启用了此步骤才适用的产品/服务。

- **测试驱动器预配 （约 30 分钟）**

在此步骤中，我们将部署和复制体验版的实例，以便它们可供客户使用。  与测试驱动器启用了此步骤才适用的产品/服务。

- **会导致管理验证和注册 (> 15 分钟)**

在此步骤中，我们确认你的潜在客户管理系统可以接收基于提供的产品/服务设置的详细信息的潜在客户。 与潜在客户管理启用了此步骤才适用的产品/服务。

- **最后一个发布 (> 30 分钟)**

在此步骤中，我们确保产品/服务在 marketplace 中成为可公开获取。

## <a name="update-existing-marketplace-offers"></a>更新现有的 marketplace 产品/服务

如果你想要对已发布的产品/服务进行更改，你将需要首先更新现有产品/服务并重新发布。

## <a name="next-steps"></a>后续步骤

- [更新商业 Marketplace 中的现有产品/服务](./update-existing-offer.md)
