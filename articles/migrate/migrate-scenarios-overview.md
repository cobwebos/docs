---
title: 将本地数据中心迁移到 Azure | Microsoft Docs
description: 阅读有关将本地数据中心迁移到 Azure 的概述白皮书。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167749"
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>将本地工作负荷迁移到 Azure


Microsoft Azure 提供了一套全面的云服务，开发者和 IT 专业人员可以使用它们借助一系列工具和框架在一个全球性的数据中心网络内构建、部署和管理应用程序。 当你的业务面临与数字化变迁相关的挑战时，Azure 云可以帮助你规划如何优化资源和运营，与客户和员工进行沟通，以及对产品进行变革。

Azure 意识到，尽管云提供了诸多优点，包括速度和灵活性、最低的成本、高性能和可靠性，但是许多组织在将来的一段时间内仍然需要运行本地数据中心。 为了破除云采用障碍，Azure 提供了混合云策略，该策略在本地数据中心与 Azure 公有云之间架起桥梁。 例如，使用 Azure 云资源（例如备份）来保护本地资源，或者使用 Azure 分析来洞察本地工作负荷。 

作为我们的混合云策略的一部分，Azure 将针对将本地应用和工作负荷迁移到云提供不断增长的解决方案。 你可以使用简单的步骤全面评估你的本地资源，进而规划它们将如何在 Azure 云中运行。 然后，根据完成的深度评估，你可以自信地将资源迁移到 Azure。 当资源已就位并在 Azure 中运行时，可以对它们进行优化来保持和提高可访问性、灵活性、安全性和可靠性。

这一系列的迁移文章展示了如何为公司规划和构建迁移策略。 这些文章展示了很多方案，我们将随时间推移逐步增加这些方案的复杂性。 下表汇总了这些方案。 对于每个方案，我们都提供了概述、迁移体系结构，并演示了迁移过程中涉及的步骤。 

**方案** | **解决方案** | **服务** | **文章** 
--- | --- | --- | ---
[方案 1：发现和评估](migrate-scenarios-assessment.md) | 发现并评估要迁移到 Azure 的本地应用、数据和基础结构 | 数据迁移助手、Azure 迁移服务  | 目前可用
**[方案 2：重新托管应用](migrate-scenarios-lift-and-shift.md)** | 将应用直接迁移到 Azure。 | Azure Site Recovery、Azure 数据库迁移服务、Azure SQL 托管实例 | 目前可用
**方案 3：重构应用** | 在迁移到 Azure 的期间重构应用。 | 正在计划 | 已计划
**方案 4：重塑应用架构** | 在迁移到 Azure 的期间重塑应用架构。 | 正在计划 | 已计划
**方案 5：重新生成应用** |在迁移到 Azure 的期间重新生成应用 | 正在计划 | 已计划




