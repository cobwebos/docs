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
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="threat-modeling-tool-feature-overview"></a>威胁建模工具功能概述

Threat Modeling Tool 可帮助满足你的威胁建模需求。 若要获取此工具的基本简介，请参阅 [Threat Modeling Tool 入门](./azure-security-threat-modeling-tool-getting-started.md)。

> [!NOTE]
>Threat Modeling Tool 会频繁更新，因此请经常查看本指南，以了解我们的最新功能和改进。

若要打开一个空页面，请选择“创建模型”。

![空页面](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

若要查看此工具中当前可用的功能，请使用[入门](./azure-security-threat-modeling-tool-getting-started.md)示例中我们的团队创建的威胁模型。

![基本威胁模型](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>导航

在探讨内置功能之前，我们来了解一下在工具中找到的主要组件。

### <a name="menu-items"></a>菜单项

此体验类似于其他 Microsoft 产品。 我们来了解顶级菜单项。

![菜单项](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| 标签                               | 详细信息      |
| --------------------------------------- | ------------ |
| **文件** | <ul><li>打开、保存和关闭文件</li><li>登录和注销 OneDrive 帐户。</li><li>共享链接（视图和编辑）。</li><li>查看文件信息。</li><li>将新模板应用于现有模型。</li></ul> |
| **编辑** | 撤消和重做操作，以及复制、粘贴和删除。 |
| **视图** | <ul><li>在“分析”和“设计”视图之间进行切换。</li><li>打开已关闭的窗口（例如模具、元素属性和消息）。</li><li>将布局重置为默认设置。</li></ul> |
| **关系图** | 添加和删除关系图，及浏览关系图的“选项卡”。 |
| **报告** | 创建与他人共享的 HTML 报告。 |
| **帮助** | 查看帮助使用工具的指南。 |

符号是指顶级菜单的快捷方式：

| 符号                               | 详细信息      |
| --------------------------------------- | ------------ |
| **打开** | 打开新文件。 |
| **保存** | 保存当前文件。 |
| **设计** | 打开设计视图，可以在其中创建模型。 |
| **分析** | 显示已发生的威胁及其属性。 |
| **添加关系图** | 添加新关系图（类似于 Excel 中的新选项卡）。 |
| **删除关系图** | 删除当前关系图。 |
| **复制/剪切/粘贴** | 复制、剪切和粘贴元素。 |
| **撤消/重做** | 撤消和重做操作。 |
| **放大/缩小** | 放大和缩小关系图以便获得更好的视觉效果。 |
| **反馈** | 打开 MSDN 论坛。 |

### <a name="canvas"></a>画布

画布是用于在其中拖放元素的空间。 拖放是生成模型的最快且最有效的方法。 也可以右键单击，然后选择菜单中的项，添加常规的元素版本，如下所示：

#### <a name="drop-the-stencil-on-the-canvas"></a>在画布上放置相应模具

![画布拖放](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>选择模具

![元素属性](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>模具

根据所选模板，可以找到所有可用的模具。 如果无法找到正确的元素，则使用其他模板。 或者可以修改模板，以满足你的需求。 通常情况下，可以找到如下所示的类别组合：

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
| **消息** | 一旦出现错误，内部工具逻辑便会向用户发出警报，例如元素之间没有数据流时。 |
| **说明** | 在整个设计和评审过程中工程团队会将手册说明添加到文件中。 |

### <a name="element-properties"></a>元素属性

随着所选元素的变化，元素属性也会有所不同。 除了信任边界外，所有其他元素都包含 3 个常规选择：

| 元素属性                               | 详细信息      |
| --------------------------------------- | ------------ |
| **Name** | 用于命名流程、存储、交互方和流，以便可以轻松识别它们。 |
| **超出范围** | 如果选择此选项，则该元素会从威胁生成矩阵中移除（不推荐）。 |
| **超出范围的原因** | 告知用户选择超出范围原因的理由字段。 |

可在每个元素类别下更改属性。 选择每个元素后可查看可用的选项。 或者可以打开模板了解详细信息。 我们来了解下其中的功能。

## <a name="welcome-screen"></a>欢迎屏幕

打开应用时，将看到“欢迎使用”屏幕。

### <a name="open-a-model"></a>打开模型

将鼠标悬停在“打开模型”上会显示 2 个选项：“从计算机打开”和“从 OneDrive 打开”。 第一个选项将打开“文件打开”屏幕。 第二个选项会将你转到 OneDrive 的登录过程。 身份验证成功后，可以选择文件夹和文件。

![打开模型](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![从计算机或 OneDrive 打开](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>反馈、建议和问题

选择“反馈、建议和问题”后，将转到 SDL 工具的 MSDN 论坛。 可以在其中阅读其他用户对该工具的看法，包括变通方法和新的想法。

![反馈](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>设计视图

打开或新建模型时，会打开“设计”视图。

### <a name="add-elements"></a>添加元素

可以使用以下两种方法在网格上添加元素：

- 拖放：将所需元素拖到网格中。 然后使用元素属性提供其他信息。
- 右键单击：右键单击网格上的任意位置，然后从下拉菜单中选择项。 所选元素的通用表示将显示在屏幕上。

### <a name="connect-elements"></a>连接元素

可以使用以下两种方法连接元素：

- 拖放：将所需的数据流拖至网格中，并将两端连接到相应的元素。
- 单击 + Shift：单击第一个元素（发送数据），按住 Shift 键，然后选择第二个元素（接收数据）。 右键单击并选择“连接”。 如果你使用的是双向数据流，顺序就不那么重要了。

### <a name="properties"></a>属性

 若要查看模具上可供修改的属性，请选择相应的模具，然后会填充相应的信息。 以下示例显示“数据库”模具拖动到关系图前后的情况：

#### <a name="before"></a>之前

![之前](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>之后

![之后](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>消息

如果在创建威胁模型时忘记将数据流连接到元素，则会收到一个通知。 可忽略此消息，或按照说明进行操作以解决此问题。 

![消息](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>说明

若要向关系图添加说明，请从“消息”选项卡切换到“说明”选项卡。

## <a name="analysis-view"></a>分析视图

生成关系图后，在快捷方式工具栏上选择“分析”符号（放大镜）可切换到“分析”视图。

![分析视图](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>已发生威胁选择

选择威胁后，可以使用三种不同的功能：

| 功能                               | 信息      |
| --------------------------------------- | ------------ |
| **已读指示器** | <p>将威胁标记为已读，可帮助你跟踪已查看的项。</p><p>![已读/未读指示器](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **交互焦点** | <p>关系图中属于威胁的交互会突出显示。</p><p>![交互焦点](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **威胁属性** | <p>有关威胁的其他信息显示在“威胁属性”窗口中。</p><p>![威胁属性](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>优先级更改

可以更改每个已发生威胁的优先级。 不同的颜色便于识别高、中和低优先级的威胁。

![优先级更改](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>威胁属性可编辑字段

如前面的图像所示，可以更改工具生成的信息。 也可以向某些字段（例如“理由”）添加信息。 这些字段由模板生成。 如果需要了解各个威胁的详细信息，可以进行修改。

![威胁属性](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>报告

完成优先级更改并更新每个已发生威胁的状态后，就可以保存文件和/或打印报表。 转到“报表” > “创建完整报表”。 为报表命名，然后应会看到类似于下图的信息：

![报表](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>后续步骤

* 若要为社区提供模板，请转到我们的 [GitHub](https://github.com/Microsoft/threat-modeling-templates) 页。 
* 若要开始使用此工具，请转到[下载](https://aka.ms/tmtpreview)页。
