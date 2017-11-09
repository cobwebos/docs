---
title: "入门 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "这是着重强调威胁建模工具操作的更深入的概述。"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>威胁建模工具入门

云和企业安全工具团队今年的早些时候发布了威胁建模工具预览版，是免费的**[单击下载](https://aka.ms/tmtpreview)**版本。 交付机制中的更改使我们能够在客户每次打开该工具时向他们推送最新的改进和 bug 修复，使其更易于维护和使用。
本文将指导你开始使用 Microsoft SDL 威胁建模方法的过程，并演示如何使用该工具来开发优秀的威胁模型，以作为安全进程的主干。

本文基于 SDL 威胁建模方法的现有认知。 若要快速查看，请参阅**[威胁建模 Web 应用程序](https://msdn.microsoft.com/library/ms978516.aspx)**和 2006 年发布的**[使用 STRIDE 方法发现安全漏洞](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** MSDN 文章的存档版本。

简而言之，此方法涉及创建关系图、识别威胁、缓解问题和验证每个缓解操作。 下面的关系图重点突出了此过程：

![SDL 进程](./media/azure-security-threat-modeling-tool/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>开始威胁建模进程

启动威胁建模工具时，将注意到下图显示的几项：

![空白起始页](./media/azure-security-threat-modeling-tool/tmtstart.png)

### <a name="threat-model-section"></a>威胁模型部分

| 组件                                   | 详细信息                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **反馈、建议和问题按钮** | 会指向有关 SDL 所有各项的 **[MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)**， 让你有机会了解其他用户在执行哪些操作，以及解决方法和建议等信息。 如果仍然找不到想要寻找的内容，请发送电子邮件至 tmtextsupport@microsoft.com，让我们的支持团队帮助你。                                                                                                                            |
| **创建模型**                          | 为你打开空白画布以绘制关系图。 请确保选出想用于你的模型的模板                                                                                                                                                                                                                                                                                                                                                                       |
| **新模型的模板**                 | 创建模型前，必须选出想要使用的模板。 我们的主要模板是 Azure 威胁模型模板，其中包含特定于 Azure 的模具、威胁和缓解操作。 对于通用模型，请从下拉菜单中选择 SDL TM 知识库。 想要创建自己的模板或为所有用户提交新的模板？ 查看我们的**[模板存储库](https://github.com/Microsoft/threat-modeling-templates)** GitHub 页以了解详细信息                              |
| **打开模型**                            | <p>打开以前保存的威胁模型。 如需打开最近经常使用的文件，“最近打开的模型”功能很实用。 将鼠标悬停在选项上方时，可以看到打开模型的两种方法：</p><p><ul><li>“从本计算机打开”  – 使用本地存储打开文件的经典方法</li><li>“从 OneDrive 打开” – 团队可以使用 OneDrive 中的文件夹在一个位置保存和共享他们的威胁模型，以帮助提升效率和协作</li></ul></p> |
| **入门指南**                   | 打开 **[Microsoft 威胁建模工具](./azure-security-threat-modeling-tool.md)**主页                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>模板部分

| 组件               | 详细信息                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **新建模板** | 打开空模板以进行构建。 除非你掌握从零开始构建模板的大量知识，否则我们建议你从现有的模板开始构建。 |
| **打开模板**       | 为你打开要进行更改的现有模板                                                                                                              |

威胁建模工具团队持续致力于改进工具的功能和提升体验。 在这一年中，可能会发生一些小的更改，但是所有主要更改都需要在指南中进行重写。 请经常参阅它以确保获取最新的公告。

## <a name="building-a-model"></a>构建模型

在本部分中，我们将讨论：

- Cristina（开发人员）
- Ricardo（计划经理）
- Ashish（测试人员）

他们将进行开发他的首个威胁模型的过程。

> Ricardo：你好 Cristina，我在研究威胁模型关系图，想要确保我们所进行的细节都没有任何问题。 你可以帮我看一下吗？
> Cristina：没问题。 让我们一起看一下。
> Ricardo 打开该工具并将他的屏幕与 Cristina 共享。

![基本威胁模型](./media/azure-security-threat-modeling-tool/basictmt.png)

> Cristina：好的，我们直接看，不过可以先向我介绍一下整体情况吗？
> Ricardo: 没问题！ 这是结构细节：
> - 我们的用户被绘制为外部实体—正方形
> - 他们正向我们的 Web 服务器发送命令—圆圈
> - Web 服务器正向数据库发出请求（两条平行线）

Ricardo 刚才向 Cristina 介绍的是 DFD，即**[数据流关系图](https://en.wikipedia.org/wiki/Data_flow_diagram)**的简写。 威胁建模工具可使用户指定信任边界（用红色虚线显示），以显示不同的实体被控制的位置。 例如，IT 管理员需要 Active Directory 系统以进行身份验证，因此 Active Directory 是不受其控制的。

> Cristina：我觉得没问题。 关于威胁呢？
> Ricardo：让我给你看一下。

## <a name="analyzing-threats"></a>分析威胁

他单击图标菜单选项（带有放大镜的文件）中的分析视图后，即转到威胁建模工具基于默认模板建立的已生成威胁的列表，该列表使用名为 **[STRIDE（欺骗、篡改、信息泄露、拒绝服务和特权提升）](https://en.wikipedia.org/wiki/STRIDE_(security))**的 SDL 方法。 思路是，软件来自可预测的一组威胁，可使用这 6 类找到。

此方法类似于通过先确保房子里的每扇门、每扇窗都锁好来保护房屋的安全，然后再添加警报或抓小偷。

![基本威胁](./media/azure-security-threat-modeling-tool/basicthreats.png)

Ricardo 先从选择列表上的第一项开始。 下面是发生的具体情况：

首先，两个模具之间的交互得到了增强

![交互](./media/azure-security-threat-modeling-tool/interaction.png)

其次，有关威胁的其他信息显示在威胁属性窗口中

![交互信息](./media/azure-security-threat-modeling-tool/interactioninfo.png)

生成的威胁帮助他了解潜在的设计缺陷。 STRIDE 分类为他提供了有关潜在的攻击途径的思路，而其他说明能够让他了解问题所在，以及缓解问题可能使用的方法。 他可以使用可编辑字段在理由详细信息中撰写理由，或根据他的组织的 bug 栏更改优先级评定。

Azure 模板具有附加详细信息，不仅帮助用户了解问题所在，还能通过向特定于 Azure 的文档添加说明、示例和超链接来帮助他们了解如何修复此问题。

该说明使用户意识到添加身份验证机制的重要性，以阻止用户被欺骗，并揭开要着手解决的第一个威胁。 与 Cristina 讨论几分钟后，他们了解了实现访问控制和角色的重要性。 Ricardo 补充了一些快速说明，以确保这些操作得以实行。

在 Ricardo 进入信息泄露下的威胁后，他意识到访问控制计划需要一些供审核和报告生成的只读帐户。 他想知道这是否是一个新威胁，但是缓解操作是相同的，所以他相应注意到了该威胁。
此外，他还进一步考虑了信息披露并意识到备份磁带需要让运行小组进行加密。

由于现有缓解或安全保证而不适用于设计的威胁可以从“状态”下拉列表中更改为“不适用”。 有三个其他选项：未启动 – 默认选择；需要调查 – 用于跟进项目；缓解 – 完全操作后。

## <a name="reports--sharing"></a>报表和共享

Ricardo 和 Cristina 查看列表并添加重要事项、缓解/理由、优先项和状态更改后，他选择“报表” -> “创建完整报表” -> “保存报表”，该操作将为他打印出一份完整的报表，让他与同事们一起查看，以确保执行了适当的安全操作。

![交互信息](./media/azure-security-threat-modeling-tool/report.png)

相反，如果 Ricardo 想要共享该文件，他可以通过在他组织的 OneDrive 帐户进行保存而轻松地实现此操作。 实现此操作后，他可以复制该文档链接并将其与他的同事共享。 

## <a name="threat-modeling-meetings"></a>威胁建模会议

当 Ricardo 使用 OneDrive 将他的威胁模型发送给他的同事后，测试人员 Ashish 并不十分欣赏。 似乎 Ricardo 和 Cristina 错过了相当重要的几个极端案例，而这极易受到威胁。 他的怀疑是对威胁模型的补充。

在此方案中，Ashish 接管威胁模型后，他发起了两个威胁建模会议：第一个会议是同步进程并介绍关系图，第二个会议是有关威胁评审和注销。

在第一个会议里，Ashish 用了 10 分钟的时间向大家介绍 SDL 威胁建模过程。 然后，他拉出威胁模型关系图并开始进行详细说明。 在 5 分钟内，已经识别出一个重要的缺失组件。

几分钟后，Ashish 和 Ricardo 就 Web 服务器构建的方式开始展开了广泛的讨论。 这不是会议继续的理想方式，但大家最终同意尽早发现差异可以在未来节省大家的时间。

在第二个会议里，团队浏览了威胁，并讨论了解决威胁的一些方法，并在威胁模型上注销。 他们将文档签入源控件并继续进行开发。

## <a name="thinking-about-assets"></a>想一想资产

进行威胁建模的某些读者可能注意到我们根本没有讨论过资产。 我们已经发现了，许多软件工程师对其软件的了解要比他们对资产概念以及攻击者可能感兴趣的资产的了解要多得多。

如果你计划针对房子做一个威胁建模，你可能开始思考你的家庭、独一无二的照片或贵重的艺术品。 可能你开始思考谁有可能侵入当前的安全系统。 或者，你可能开始考虑物理功能，如水池或前门廊。 考虑资产、攻击者或软件设计时，这些方法都很类似。 以上这三种方法都适用。

我们在此处介绍的威胁建模的方法基本上比 Microsoft 过去使用的方法要简单得多。 我们发现软件设计方法对我们的团队很适用。 我们希望该方法对你也适用。

## <a name="next-steps"></a>后续步骤

将你的问题、评论和疑问发送至 tmtextsupport@microsoft.com。**[下载](https://aka.ms/tmtpreview)**威胁建模工具以开始。