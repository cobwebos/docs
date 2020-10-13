---
title: CAF 基础蓝图示例概述
description: 适用于 Azure 的云采用框架 (CAF) 基础蓝图示例的概述和体系结构。
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: 77e8b79ec7cf217161099808cee4364e31c6d6dd
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950272"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>适用于 Azure 的 Microsoft 云采用框架基础蓝图示例概述

适用于 Azure 的 Microsoft 云采用框架 (CAF) 基础蓝图部署了你的第一个生产级别 Azure 应用程序所需的一组核心基础结构资源和策略控制。 此基础蓝图基于在 CAF 中找到的建议模式。

## <a name="architecture"></a>体系结构

CAF 基础蓝图示例在 Azure 中部署建议的基础结构资源，这些资源可供组织用来将管理其云资产所需的基础控制实施到位。 此示例将部署并强制实施资源、策略和模板，从而使组织能够自信地开始使用 Azure。

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="C A F 基础，图像说明了作为 C A F 指南的一部分安装（以便为开始使用 Azure 创建基础）的内容。" border="false":::
   介绍 Azure 体系结构，该体系结构是通过部署 C A F 基础蓝图来实现的。  它适用于具有资源组的订阅，该订阅包含用于存储日志的存储帐户、Log analytics 配置为在存储帐户中存储。 它还介绍了如何使用 Azure 安全中心标准设置配置 Azure Key Vault。 所有这些核心基础结构均使用 Azure Active Directory 进行访问，并使用 Azure Policy 强制实施。     
:::image-end:::

此实现纳入多项 Azure 服务，这些服务用于提供安全的、全面受监视的、面向企业的基础。 此环境包括：

- 一个 [Azure Key Vault](../../../../key-vault/general/overview.md) 实例，用于托管对共享服务环境中部署的 VM 使用的机密
- 部署 [Log Analytics](../../../../azure-monitor/overview.md)，以便确保从开始安全部署起所有操作和服务都记录到一个中心位置的[存储帐户](../../../../storage/common/storage-introduction.md)，用于诊断日志记录
- 部署 [Azure 安全中心](../../../../security-center/security-center-introduction.md)（标准版），从而为已迁移的工作负荷提供威胁防护
- 蓝图也定义和部署了 [Azure Policy](../../../policy/overview.md) 定义：
  - 策略定义：
    - 应用于资源组的标记功能 (CostCenter)
    - 使用 CostCenter 标记追加资源组中的资源
    - 资源和资源组允许的 Azure 区域
    - 允许的存储帐户 SKU（在部署时选择）
    - 允许的 Azure VM SKU（在部署时选择）
    - 要求部署网络观察程序 
    - 要求 Azure 存储帐户安全传输加密
    - 拒绝资源类型（在部署时选择）  
  - 策略计划：
    - 在 Azure 安全中心启用监视（100+ 个策略定义）

所有这些元素遵守 [Azure 体系结构中心 - 参考体系结构](/azure/architecture/reference-architectures/)中发布的行之有效的做法。

> [!NOTE]
> CAF 基础布设了用于工作负荷的基础体系结构。
> 你仍需要部署此基础体系结构后面的工作负荷。

有关详细信息，请参阅[适用于 Azure 的 Microsoft 云采用框架 - 就绪](/azure/cloud-adoption-framework/ready/)。

## <a name="next-steps"></a>后续步骤

你已查看了 CAF 基础蓝图示例的概述和体系结构。

> [!div class="nextstepaction"]
> [CAF 基础蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。