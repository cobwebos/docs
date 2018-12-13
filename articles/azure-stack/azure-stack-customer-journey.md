---
title: Azure Stack 数据中心集成演练 |Microsoft Docs
description: 了解 Azure Stack 现场数据中心内的成功部署的预期行为。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.openlocfilehash: db33deaa9bb0bf9a4c3f766fbae883df4075c431
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323365"
---
# <a name="azure-stack-customer-journey"></a>Azure Stack 客户旅程

本指南介绍了从采购到成功的现场部署解决方案提供程序通过一个集成的解决方案的端到端 Azure Stack 客户体验。 简化之旅并帮助设置上，作为 Azure Stack 客户，应预期将 Azure Stack 集成到你的数据中心时，请使用此信息。

作为 Azure Stack 客户，应预计到以下数据中心集成几个阶段：

|     |规划阶段|订单流程|预先部署|工厂过程|硬件交付|现场部署|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|与合作伙伴合作，提供售前支持。|准备软件许可和根据需要的约定。|提供所需的工具来收集到客户的数据中心集成要求和文档。|每月的频率提供最新基线版本和工具链更新。|不适用|Microsoft 支持工程师帮助进行任何部署问题。|
|**合作伙伴**|建议根据客户需求的解决方案选项。<br><br>如有必要，请提出概念证明 (POC)。<br><br>建立业务关系。<br><br>决定的支持级别。|使用客户准备必要的合同。<br><br>创建客户采购订单。<br><br>决定在交付时间线上。<br><br>如有必要，请与 Microsoft 联系客户。|向客户提供必要的培训，以确保了解所有的部署先决条件和数据中心集成选项。<br><br>帮助客户收集的数据验证以确保完整性和精确性。|将应用最后一次验证的基线生成。<br><br>将应用所需的 Microsoft 部署工具包。|寄送到客户站点的硬件。|部署由现场工程师。<br><br>机架和堆栈。<br><br>硬件生命周期主机 (HLH) 部署。<br><br>Azure Stack 部署。<br><br>移交给客户。|
|**客户**|介绍预期的用例，并指定要求。|确定要使用、 审阅和批准协定的计费模型。|完成部署工作表，并确保所有部署先决条件都都满足并准备好进行部署。|不适用|通过确保所需的所有 power 准备数据中心和冷却、 边界连接和其他所需的数据中心集成要求已到位。|提供订阅凭据，并支持提供的数据有关的问题是否部署过程中在可用。|
| | | | | | | |


## <a name="planning-phase"></a>规划阶段
Microsoft 或 Azure Stack 解决方案合作伙伴，将工作与你一同评估并了解你的需求来确定 Azure Stack 是否适合你的解决方案时，规划阶段：

它们将帮助你在以下决定：

-   是 Azure Stack 为你的组织的正确解决方案？

-   你将需要哪种大小解决方案？

-   哪种类型的计费和许可模型适合你的组织？

-   电源和冷却要求有哪些？

若要确保硬件解决方案将最适合您的需求， [Azure Stack 容量规划器](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)可用于帮助预购计划，以确定适当容量和 Azure Stack 硬件的配置解决方案。

该电子表格*不*用作您自己的调查和分析的硬件解决方案最适合您的需要的替代。 在规划 Azure Stack 部署，还应查看[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)适用于 Azure Stack 集成系统。

## <a name="order-process-phase"></a>订单处理阶段
在此阶段，许多您可行性方面的问题会有人回答了。 现在，你已准备好提交到购买 Azure Stack，签名所需的所有协定后并采购订单，您将需要向解决方案提供商提供的集成要求数据。

## <a name="pre-deployment-phase"></a>预先部署阶段
在此阶段，您需要决定你想要将 Azure Stack 集成到你的数据中心。 为了简化此过程，Microsoft，与解决方案提供商，协作将汇总到要求模板可帮助您收集所需的信息来规划集成的系统部署在环境中。

[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)文章提供了可帮助你完成模板，称为部署工作表的信息。 

> [!IMPORTANT]
> 在此阶段中非常重要，调查和决定在排序解决方案之前所有必备信息。 请注意，此步骤非常耗时，需要协调和数据收集从您的组织内的多个领域。 

在预先部署阶段中将需要以下决定：

- **Azure Stack 连接模型和标识提供程序**。 您可以选择或者部署 Azure Stack[连接到 internet （和 Azure） 或断开](azure-stack-connection-models.md)。 若要获取最大效益，从 Azure Stack，包括混合方案中，你想要部署连接到 Azure。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 **无需重新部署整个系统，不能以后更改此**。

- **许可模型**。 你可以从选择的许可模型选项取决于部署将具有的类型。 在标识提供者选项没有任何影响租户虚拟机或标识系统和使用的帐户上。
    - 中的客户[断开连接部署](azure-stack-disconnected-deployment.md)具有只有一个选项： 基于容量的计费。

    - 中的客户[连接的部署](azure-stack-connected-deployment.md)可以基于容量的计费和作为您的使用付费之间进行选择。 基于容量的计费将需要企业协议 (EA) Azure 订阅进行注册。 此注册所需的它提供用于通过 Azure 订阅 Marketplace 中的项的可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)的部署、 操作和管理 Azure Stack 系统至关重要。 有几个注意事项，请转到确保 Azure Stack 解决方案可复原且高度可用的物理基础结构以支持其操作。

- **防火墙集成**。 建议你[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack。 防火墙有助于防止 DDOS 攻击，入侵检测和内容检查。 但是，应注意，它可以成为 Azure 存储服务的吞吐量瓶颈。


- **证书要求**。 很重要的所有[必需的证书](azure-stack-pki-certs.md)可用*先前*到达数据中心，实现部署的现场工程师。

所有系统必备信息收集之后通过在部署工作表，解决方案提供商将启动工厂过程基于收集的数据以确保你的数据中心与 Azure Stack 的成功集成。

## <a name="hardware-delivery-phase"></a>硬件交付阶段
解决方案提供商将与你一起安排解决方案将为您的设施到达时。 一旦收到并实施措施，将需要向解决方案提供商，让工程师提供现场执行 Azure Stack 部署计划时间。

它是**重要**必备的所有数据都已锁定并可用*之前现场工程师到达时部署解决方案*。

-   所有的证书必须购买并准备就绪。

-   必须确定域名。

-   网络集成的所有参数将完成，并且与你已与你的解决方案提供商共享的内容相匹配。

> [!TIP]
> 如果此信息的任何已更改，请确保之前计划在实际部署的解决方案提供程序通信更改。

## <a name="onsite-deployment-phase"></a>现场部署阶段
若要部署 Azure Stack，将需要从硬件解决方案提供商的现场工程师来启动部署。 若要确保成功部署，请确保通过在部署工作表提供的所有信息未都更改。 

下面是应预期从现场工程师部署体验期间：

- 检查所有电缆和边框连接以确保解决方案正确结合在一起，并能满足您的要求
- 配置解决方案 HLH （硬件生命周期主机）
- 检查以确保所有 BMC、 BIOS 和网络设置正确无误。
- 请确保所有组件的固件是由该解决方案的最新已批准版本
- 启动部署

> [!NOTE]
> 通过现场工程师的部署过程可能需要一种商业周刊 》 才能完成。

## <a name="post-integration-phase"></a>后集成阶段
解决方案移交给客户后集成阶段之前，必须由合作伙伴执行几个步骤。 在此阶段中，验证是一定要确保系统的部署和执行正确。 

OEM 合作伙伴应执行的操作包括：

-   [运行 test-azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [注册到 Azure](azure-stack-registration.md)

-   [Marketplace 联合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   备份的交换机配置文件

-   删除 DVM

-   为部署准备摘要的客户

-   [检查以确保解决方案软件更新到最新版本的更新](azure-stack-updates.md)

有几个必需或可选具体取决于安装类型的步骤。

-   如果部署已完成使用[AD FS](azure-stack-integrate-identity.md)，然后戳将需要与客户集成 Azure Stack 的自己的 AD FS。

  > [!NOTE]
  > 此步骤中负责的客户，尽管可以根据需要选择合作伙伴提供服务来执行此操作。

-   与现有的监视系统从相应合作伙伴的集成。

    -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)还支持车队管理功能。

    -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>整体时间线

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack，使 Azure 一致的、 涵盖完整系统生命周期的集成的支持体验。 若要完全支持 Azure Stack 集成系统，客户需要两个支持协定;一个与 Microsoft （或其云解决方案提供商） 用于 Azure 服务支持，一个用于系统支持的硬件提供程序。 集成的支持体验提供了协作的问题升级和解决方法，以便客户可获得一致的支持体验，无论其它们首先要调用。 对于已安装了高级客户，客户 Azure 的标准 / 包含了与 Microsoft Azure Stack 软件支持的 ProDirect 或合作伙伴支持。

集成的支持体验使利用用例 Exchange 支持案例和事例更新的 Microsoft 硬件合作伙伴之间的双向传输机制。 Microsoft Azure Stack 将遵循[Modern Lifecycle 策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
详细了解如何[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)。
