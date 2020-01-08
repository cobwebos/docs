---
title: Azure 安全基准简介
description: 安全基准概述简介
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2d0cceca841a2aff2538dcf8763d245ca8cfd13d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564356"
---
# <a name="azure-security-benchmarks-introduction"></a>Azure 安全基准介绍

你可能有多年甚至数十年的本地计算经验。 你知道如何保护这些部署。 但云有所不同。 你如何了解你的云部署是否安全？ 本地系统的安全做法和云部署的安全实践之间有何区别？

有大量的白皮书、最佳做法、参考体系结构、web 指南、开源工具、商业解决方案、智能源等，可用于帮助保护云。 应该使用哪个选项？ 在云中获取可接受的安全级别是什么？ 

保护云部署的最佳方式之一是重点关注云安全基准建议。 保护任何服务的基准建议从对网络安全风险的基本了解开始，并对其进行管理。 然后，你可以使用云服务提供商提供的基准安全建议来帮助你在环境中选择特定的安全配置设置，从而使用此理解。 

Azure 安全基准包括一系列可用于帮助保护在 Azure 中使用的大多数服务的高影响安全建议。 可以将这些建议视为 "常规" 或 "组织"，因为它们适用于大多数 Azure 服务。 然后针对每个 Azure 服务自定义 Azure 安全基准建议，此自定义指南包含在服务建议文章中。 

Azure 安全基准文档指定安全控制和服务建议。

- **安全控制**： Azure 安全基准建议按安全控制进行分类。 安全控件表示不可知的高级供应商安全要求，例如网络安全和数据保护。 每个安全控件都有一组安全建议和说明，可帮助您启用这些建议。 
- **服务建议**：在可用时，针对 azure 服务的基准建议将包含为服务定制的 Azure 安全基准建议，以及特定服务特有的其他建议。 

术语 "控件"、"基准" 和 "基准" 通常在 Azure 安全基准文档中使用，并且必须了解 Azure 如何使用这些术语。 

| 条款 | Description | 示例 |
|--|--|--|
| 控制 | **控件**是需要解决的功能或活动的高级说明，并不是特定于技术或实现的。 | 数据保护是其中一项安全控制措施。 此控件包含需要解决的特定操作，以帮助确保数据受到保护。 |
| 基准 | **基准测试**包含针对特定技术的安全建议，例如 Azure。 建议按其所属的控件进行分类。 | Azure 安全基准包括特定于 Azure 平台的安全建议  |
| 基线 | **基线**是组织的安全要求。 安全要求基于基准建议。 每个组织决定要将哪些基准建议纳入其基准。 | Contoso 公司通过选择需要 Azure 安全基准中的特定建议来创建其安全基线。 |

欢迎你提供有关 Azure 安全基准的反馈！ 建议你在以下反馈区域提供注释。 如果你更愿意使用 Azure 安全基准团队与 Azure 安全基准团队共享你的输入，欢迎在 https://aka.ms/AzSecBenchmark 填写表单 