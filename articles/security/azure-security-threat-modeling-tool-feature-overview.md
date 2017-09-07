---
title: "Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "了解威胁建模工具中提供的所有功能"
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
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>威胁建模工具功能概述

很高兴你选择使用威胁建模工具，它可以满足你的各项威胁建模需求！ 如果你尚未使用过此工具，请访问[威胁建模工具入门](./azure-security-threat-modeling-tool-getting-started.md)，以了解相关基础知识。

> 该工具会频繁更新，因此请经常查看本指南，以了解我们的最新功能和改进。

单击“创建新模型”按钮将打开一个空白起始页，类似下图所示：

![空白起始页](./media/azure-security-threat-modeling-tool/tmtstart.png)

[入门](./azure-security-threat-modeling-tool-getting-started.md)示例中使用的是我们团队创建的威胁模型，我们来看下此工具当前可用的所有功能。

![基本威胁模型](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>导航

在深入探讨内置功能之前，我们来看下在工具中找到的主要组件

### <a name="menu-items"></a>菜单项

此体验应类似于其他 Microsoft 产品。 让我们从顶级菜单项开始：

![菜单项](./media/azure-security-threat-modeling-tool/menuitems.png)

| 标签                               | 详细信息      |
| --------------------------------------- | ------------ |
| **文件** | <ul><li>打开、保存和关闭文件</li><li>登录/注销 OneDrive 帐户</li><li>共享链接（视图 + 编辑）</li><li>查看文件信息</li><li>将新模板应用于现有模型</li></ul> |
| **编辑** | 撤消/重做操作，以及复制、粘贴和删除 |
| **视图** | <ul><li>在“分析”和“设计”视图之间进行切换</li><li>打开已关闭的窗口（例如模具、元素属性和消息）</li><li>将布局重置为默认设置</li></ul> |
| **关系图** | 添加/删除关系图和浏览关系图的“选项卡” |
| **报告** | 创建与他人共享的 HTML 报告 |
| **帮助** | 帮助使用工具的指南 |

图标是顶级菜单的快捷方式：

| 图标                               | 详细信息      |
| --------------------------------------- | ------------ |
| **打开** | 打开新文件 |
| **保存** | 保存当前文件 |
| **设计** | 进入设计视图，可以在其中创建模型 |
| **分析** | 显示已发生的威胁及其属性 |
| **添加关系图** | 添加新关系图（类似于 Excel 中的新选项卡） |
| **删除关系图** | 删除当前关系图 |
| **复制/剪切/粘贴** | 复制/剪切/粘贴元素 |
| **撤消/重做** | 撤消/重做操作 |
| **放大/缩小** | 放大和缩小关系图以便获得更好的视觉效果 |
| **反馈** | 打开 MSDN 论坛 |

### <a name="canvas"></a>画布

将元素拖放至其中的空间。 拖放是生成模型的最快且最有效的方法。 还可以右键单击从菜单中选择，以此增加你所使用的元素的通用版本，如下所示。

#### <a name="dropping-the-stencil-on-the-canvas"></a>在画布上放置相应模具

![画布拖放](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>单击相应模具

![元素属性](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>模具

根据所选模板，可以在其中找到所有可用的模具。 如果找不到正确的元素，请尝试使用另一个模板，或修改模板以适应你的需求。 通常情况下，应该能够找到如下所示的类别组合：

| 模具名称                               | 详细信息      |
| --------------------------------------- | ------------ |
| **过程** | 应用程序、浏览器插件、线程、虚拟机 |
| **外部交互方** | 身份验证提供程序、浏览器、用户、Web 应用程序 |
| **数据存储** | 缓存、存储、配置文件、数据库、注册表 |
| **数据流** | 二进制、ALPC、HTTP、HTTPS/TLS/SSL、IOCTL、IPSec、命名管道、RPC/DCOM、SMB、UDP |
| **信任行/边框边界** | 企业网络、Internet、计算机、沙盒、用户/内核模式 |

### <a name="notesmessages"></a>说明/消息

| 组件                               | 详细信息      |
| --------------------------------------- | ------------ |
| **消息** | 一旦出现错误，内部工具逻辑便会向用户发出警报，例如元素之间没有数据流时 |
| **说明** | 由工程团队在整个设计和评审过程中添加到文件中的手册说明 |

### <a name="element-properties"></a>元素属性

这些属性会因所选元素而异。 除了信任边界外，所有其他元素都包含 3 个常规选择：

| 元素属性                               | 详细信息      |
| --------------------------------------- | ------------ |
| **Name** | 用于命名流程、存储、交互方和流，以便轻松识别 |
| **Out of Scope** | 如果选择此选项，则该元素会从威胁生成矩阵中移除（不推荐） |
| **Reason for Out of Scope** | 告知用户选择超出范围原因的理由字段 |

可在每个元素类别下更改属性。 单击每个元素检查可用选项，或打开模板了解详情。 让我们来探讨一下这些功能。

## <a name="welcome-screen"></a>欢迎屏幕

打开应用时，首先看到的是欢迎屏幕。

### <a name="open-a-model"></a>打开模型

悬停在“打开模型”按钮会显示 2 个隐藏的选项：“从计算机打开”和“从 OneDrive 打开”。 第一个会打开“文件打开”屏幕，而第二个选项则会打开 OneDrive 的登录过程，在成功进行身份验证之后可以选取文件夹和文件。

![打开模型](./media/azure-security-threat-modeling-tool/openmodel.png)

![从计算机或 OneDrive 打开](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>反馈、建议和问题

选择此选项会使你转到 SDL 工具的 MSDN 论坛。 可以通过此方法很好地了解其他用户对该工具的看法，包括变通方法和新的想法。

![反馈](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>设计视图

无论何时打开或创建新模型，都将转到设计视图中。

### <a name="adding-elements"></a>添加元素

可以通过以下两种方法在网格中添加元素：

- 拖放 – 将所需的元素拖动到网格中，然后使用元素属性提供其他信息。
- 右键单击 – 右键单击网格上的任意位置，然后从下拉菜单中选择。 该元素的通用表示将显示在屏幕上。

### <a name="connecting-elements"></a>连接元素

可以通过以下两种方法来连接工具中的元素：

- 拖放 – 将所需的数据流拖至网格中，并将两端连接到相应的元素。
- 单击 + Shift – 单击第一个元素（发送数据），按住 Shift 键，然后选择第二个元素（接收数据）。 右键单击，然后选择“连接”。 如果你使用的是双向数据流，顺序就不那么重要了。

### <a name="properties"></a>属性

显示可以在关系图中放置的模具上修改的所有属性。 若要查看属性，只需单击模具即可填充相应信息。 以下示例显示“数据库”模具拖动到关系图前后的情况：

#### <a name="before"></a>之前

![之前](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>之后

![之后](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>消息

如果在创建威胁模型时忘记将数据流连接到元素，则消息窗口将通知你执行此操作。可以选择忽略，或按照说明进行操作以解决此问题。 

![消息](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>说明

从“消息”选项卡切换到“说明”，可让你将注释添加到关系图中以获取所有想法

## <a name="analysis-view"></a>分析视图

完成生成关系图后，通过转到顶部菜单选择，并选择绘图调色板旁边的放大镜以切换到分析视图。

![分析视图](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>已发生威胁选择

单击威胁时，可以利用以下三个唯一功能：

| 功能                               | 信息      |
| --------------------------------------- | ------------ |
| **已读指示器** | <p>威胁现在标记为已读，这易于帮助你跟踪已完成的项</p><p>![已读/未读指示器](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **交互焦点** | <p>关系图中属于威胁的交互会突出显示</p><p>![交互焦点](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **威胁属性** | <p>有关威胁的其他信息会填充威胁属性窗口</p><p>![威胁属性](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>优先级更改

更改每个已发生威胁的优先级也会更改其颜色，以便容易识别高、中和低优先级的威胁。

![优先级更改](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>威胁属性可编辑字段

如上图所示，用户可以更改由工具生成的信息，还可以将信息添加到某些字段，例如理由。 这些字段都由该模板生成，因此，如果你需要每个威胁的详细信息，最好进行修改。

![威胁属性](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>报告

一旦完成优先级更改并更新每个已发生威胁的状态，就可以通过转到“报表”和“创建完整报表”来保存文件和/或打印报表。 系统将要求你命名报表，之后，应该会看到类似如下图所示的内容：

![报表](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>后续步骤

若要为社区提供模板，请转到我们的 [GitHub](https://github.com/Microsoft/threat-modeling-templates) 页。 [下载](https://aka.ms/tmtpreview)该工具以立即开始。

