---
title: Microsoft Threat Modeling Tool 概述 - Azure
description: 概述 Microsoft Threat Modeling Tool，其中包含有关如何开始使用该工具的信息（包括威胁建模过程）。
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: dcf782a41dbb09135f7a4cd767a94ff90578b6ba
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169355"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft 威胁建模工具

威胁建模工具是 Microsoft 安全开发生命周期 (SDL) 的核心要素。 当潜在安全问题处于无需花费过多成本即可相对容易解决的阶段，软件架构师可以使用威胁建模工具提前识别这些问题。 因此，它能大幅减少开发总成本。 此外，我们设计该工具时考虑到了非安全专家的体验，为他们提供有关创建和分析威胁模型的清晰指导，让所有开发人员都可以更轻松地使用威胁建模。 

任何人都可以使用该工具来实现以下目的：

* 交流系统的安全设计
* 使用经过证实的方法分析这些设计是否存在潜在安全问题
* 建议和管理针对安全问题的缓解措施

下面只是该工具的一部分功能和创新：

* **自动化：** 有关绘制模型的指导和反馈
* **STRIDE per Element：** 引导式威胁分析和缓解措施
* **报表：** 验证阶段的安全活动与测试
* **唯一方法：** 使用户能够更好地直观了解威胁
* **专为开发人员设计，以软件为中心：** 许多方法是以资产或攻击者为中心。 我们是以软件为中心。 我们的解决方案构建在所有软件开发人员和架构师都很熟悉的活动基础之上 - 例如，为软件体系结构绘图
* **注重设计分析：** 术语“威胁建模”可以指需求，也可以指设计分析技术。 有时，它指的是两者的复杂混合形式。 Microsoft SDL 的威胁建模方法是一种有重点的设计分析技术

## <a name="next-steps"></a>后续步骤

下表包含可帮助你开始 Threat Modeling Tool 的重要链接：另请参阅：[系统要求](threat-modeling-tool-releases.md)

| 步骤  | 说明                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [下载威胁建模工具](https://aka.ms/threatmodelingtool)                                |
| **2** | [阅读入门指南](threat-modeling-tool-getting-started.md)    |
| **3** | [熟悉功能](threat-modeling-tool-feature-overview.md)   |
| **4** | [了解有关已发生威胁的类别](threat-modeling-tool-threats.md)   |
| **5** | [查找已发生威胁的缓解措施](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>资源

下面是几篇较旧的文章，仍然与目前的威胁建模相关：

* [有关威胁建模的重要性的文章](https://docs.microsoft.com/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [可信任计算发布的培训](https://www.microsoft.com/download/details.aspx?id=16420)

查看一些威胁建模工具专家已完成的作品：

* [威胁管理器](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi 安全博客](https://simoneonsecurity.com/)
