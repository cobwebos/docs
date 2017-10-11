---
title: "入门教程-Microsoft 威胁建模工具的 Azure |Microsoft 文档"
description: "这是突出显示中操作的威胁建模工具的更深入概述。"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 2d940b42108948f4cd36a585f1e79def05fe8fd3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>威胁建模工具入门

云和企业的安全工具团队发布了威胁建模工具预览版本年度早些时候为可用**[单击下载](https://aka.ms/tmtpreview)**。 传送机制中的更改使得我们可以将推送的最新改进和 bug 修复到客户每次打开该工具，使其更易于维护和使用。
本文将指导您完成如何开始使用 Microsoft SDL 威胁建模的方法的过程，并演示如何使用该工具作为安全过程的主干开发很好的威胁模型。

本文基于 SDL 威胁建模的方法的现有认知。 有关快速评论，请参阅**[威胁建模 Web 应用程序](https://msdn.microsoft.com/library/ms978516.aspx)**和已存档的版本**[使用 STRIDE 方法发现安全缺陷](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** 2006 年发布的 MSDN 文章。

快速总之，此方法涉及按创建一个关系图、 识别威胁、 减少这些和验证每个缓解。 下面是突出显示了此过程的关系图：

![SDL 过程](./media/azure-security-threat-modeling-tool/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>启动威胁建模的过程

在启动威胁建模工具，您将注意到的几种方法，如图中所示：

![空白起始页](./media/azure-security-threat-modeling-tool/tmtstart.png)

### <a name="threat-model-section"></a>威胁模型部分

| 组件                                   | 详细信息                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **反馈和问题按钮** | 将你带 **[MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)**的所有方面 SDL。 它使您有机会通读其他用户做什么，以及解决方法和建议。 如果仍然找不到你正在寻找的内容，发送电子邮件tmtextsupport@microsoft.com有助于我们支持团队                                                                                                                            |
| **创建模型**                          | 打开用于你要绘制关系图的空白画布。 请确保选择你想要为你的模型使用的模板                                                                                                                                                                                                                                                                                                                                                                       |
| **新的模型的的模板**                 | 必须选择要在创建模型之前使用的模板。 我们的主模板是 Azure 威胁模型模板的说明进行操作，其中包含特定于 Azure 的模具、 威胁和缓解措施。 对于泛型模型，从下拉列表菜单中选择 SDL TM 知识库。 要创建你自己的模板或提交一个新的所有用户？ 签出我们**[模板存储库](https://github.com/Microsoft/threat-modeling-templates)** GitHub 页以了解详细信息                              |
| **打开模型**                            | <p>此时将打开以前保存的威胁模型。 如果你需要打开最新文件，则最近打开的模型功能非常用。 当你将鼠标悬停在所选内容时，你将找到通过 2 种方法打开模型：</p><p><ul><li>打开从此计算机 – 打开使用本地存储的文件的经典方法</li><li>打开从 OneDrive – 团队可使用在 OneDrive 中的文件夹来保存和共享中单个位置有助于提高工作效率和协作的所有其威胁模型</li></ul></p> |
| **快速入门指南**                   | 打开 **[Microsoft 威胁建模工具](./azure-security-threat-modeling-tool.md)**主页                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>模板部分

| 组件               | 详细信息                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **创建新的模板** | 将打开你在生成的空白模板。 除非你有大量知识库中生成从零开始的模板，我们建议你根据现有的生成 |
| **打开模板**       | 打开现有模板，你可以对注册表进行更改                                                                                                              |

威胁建模工具团队不断努力以改进工具功能和体验。 一些小的更改，可能会发生在过程中的一年中，但所有主要的更改则要求指南中的重写操作。 请参阅通常以确保获取最新通告。

## <a name="building-a-model"></a>生成模型

在本部分中，我们执行：

- Cristina （开发人员）
- Ricardo （程序管理器） 和
- Ashish （测试）

将完成开发其第一个的威胁模型的过程。

> Ricardo： 您好 Cristina，我处理的威胁模型关系图，以及想要确保我们获得的详细信息正确。 你可以帮助我查看它？
> Cristina： 绝对。 让我们了解。
> Ricardo 打开该工具，并与 Cristina 共享他的屏幕。

![基本的威胁模型](./media/azure-security-threat-modeling-tool/basictmt.png)

> Cristina: 确定，如下所示简单，但可以在演练我它？
> Ricardo： 确保 ！ 此处是进行细分：
> - 我们人类用户绘制为一个外部实体-正方形
> - 它们要将命令发送给我们的 Web 服务器 — 圆
> - Web 服务器咨询数据库 （两条平行直线）

什么 Ricardo 刚才介绍 Cristina 是 DFD，短，无法用于**[数据流关系图](https://en.wikipedia.org/wiki/Data_flow_diagram)**。 威胁建模工具允许用户指定信任边界，由红色虚线，以显示不同的实体在控件中的位置。 例如，IT 管理员需要 Active Directory 系统进行身份验证，因此 Active Directory 是受其控制。

> Cristina： 外观给我。 关于威胁的新增功能？
> Ricardo： 让我向您展示。

## <a name="analyzing-threats"></a>分析的威胁

一旦用户单击使用 SDL 方法称作在图标菜单选择 （带有文件放大镜），他转到在基于默认模板中，生成威胁找到的威胁建模工具的列表中的分析视图 **[STRIDE （欺骗、 篡改、 信息泄露、 拒绝服务和特权提升）](https://en.wikipedia.org/wiki/STRIDE_(security))**。 思路是软件提供可预测的一组使用这些 6 类别可以找到的威胁。

这种方法是像保护你在家通过确保每个门和窗口已锁定机制有将警报系统添加或后盗贼追踪之前。

![基本的威胁](./media/azure-security-threat-modeling-tool/basicthreats.png)

Ricardo 开始通过选择列表上的第一项。 下面是发生的具体情况：

首先，增强两个模具之间的交互

![交互](./media/azure-security-threat-modeling-tool/interaction.png)

有关的威胁的第二个、 其他信息显示在威胁属性窗口

![交互信息](./media/azure-security-threat-modeling-tool/interactioninfo.png)

生成的威胁可帮助他了解潜在的设计缺陷。 STRIDE 分类，他可以拥有上潜在的攻击媒介，了解时的其他说明告诉完全什么是错误，以及为了缓解问题的可能方法。 他可以使用可编辑字段编写理由详细信息中的说明或更改优先级级别，具体取决于其组织的 bug 栏。

Azure 模板具有更多详细信息以帮助用户了解问题的原因，不仅能还如何修复此错误通过将说明、 示例和超链接添加到特定于 Azure 的文档。

描述进行他意识到添加的身份验证机制，以防止用户的欺骗的重要性泄露的首要威胁要进行处理。 几分钟到与 Cristina，讨论它们理解实现访问控制和角色的重要性。 Ricardo 填充一些快速说明，请确保这些功能已实现的。

如 Ricardo 进入下信息泄露威胁，他认识到访问控制计划生成所需某些只读的帐户的审核和报告。 他想知道是否这应是一个新的威胁，但缓解措施是相同的因此他相应地注明威胁。
他还考虑信息泄露更位，并实现备份的磁带已将需要加密，运营团队的作业。

由于现有的缓解措施或安全的设计不适用的威胁保证可以更改为"不适用"从状态下拉列表。 有三个其他选项： 未启动 – 默认选择，需要调查 – 用于执行后续操作项和缓解 – 后完全处理的。

## <a name="reports--sharing"></a>报表和共享

一旦 Ricardo 经历 Cristina 列表，并添加重要说明、 缓解措施/理由、 优先级和状态更改，他选择报表-> 的创建完整的报表-> 保存报表，他将经历与同事，以确保实现适当的安全性工作良好报表打印。

![交互信息](./media/azure-security-threat-modeling-tool/report.png)

如果 Ricardo 想要改为共享该文件，他可以轻松完成此操作保存在其组织的 OneDrive 帐户中。 一旦他做的这些，他可以复制文档链接，并将其与他的同事共享。 

## <a name="threat-modeling-meetings"></a>威胁建模会议

已 underwhelmed Ricardo 发送到他的同事使用 OneDrive、 Ashish、 测试人员他的威胁模型时。 看起来像 Ricardo 和 Cristina 丢失相当多的重要角情况下，无法轻易受到损害。 他怀疑是对威胁模型的补充。

在此方案中之后 Ashish 夺取威胁模型中，, 他为调用两个威胁建模会议： 一个会议同步进程上并遍历关系图，然后选择对威胁第二个会议查看和注销。

在第一个会议中，Ashish 花费 10 分钟遍历每个人通过 SDL 威胁建模的过程。 他然后上威胁模型关系图移至超类并启动详细说明。 在 5 分钟内已经确定缺少一个重要组成部分。

几分钟的时间更高版本，Ashish 和 Ricardo 排入的 Web 服务器如何生成的扩展讨论。 它不是理想方式的会议若要继续，但每个人都最终议定，尽早发现差异即将保存这些时间在将来。

在第二个会议中，团队一下威胁、 讨论解决它们的一些方法和注销威胁模型。 它们签入源控件的文档，而与开发继续执行。

## <a name="thinking-about-assets"></a>考虑资产

具有威胁建模某些读者可能会注意到，我们尚未讨论根本有关资产。 我们已经了解许多软件工程师不是其了解的资产的概念和哪些资产攻击者可能会对感兴趣更好地了解他们的软件。

如果你将要转到威胁模型内部，你可以开始通过考虑系列、 不可替代的照片或有价值的图片。 可能是你可以开始考虑人员可能会中断中和当前的安全系统。 或者，您可以通过考虑的物理功能，如池或前端 porch 开始。 这些是类似于考虑资产、 攻击者或软件设计的。 所有三种工作。

威胁的建模我们在此处介绍的方法是在过去，Microsoft 已经做什么比简单得多。 我们找到软件设计方法非常适用于很多团队。 我们希望包括你的帐户。

## <a name="next-steps"></a>后续步骤

发送你的问题、 注释和考虑事项tmtextsupport@microsoft.com。**[下载](https://aka.ms/tmtpreview)**威胁建模工具，若要开始。