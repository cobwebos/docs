---
title: 用于 VM 的 Azure Monitor（预览版）的已知问题 | Microsoft Docs
description: 本文介绍了用于 VM 的 Azure Monitor 的已知问题。用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它组合了 Azure VM 操作系统的运行状况、应用程序依赖项发现和性能监视功能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: 711b3707d536c4858578817589670edf0f467b64
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670723"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor（预览版）的已知问题

本文介绍了用于 VM 的 Azure Monitor 的已知问题。用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它组合了 Azure VM 操作系统的运行状况、应用程序组件发现和性能监视功能。 

## <a name="health"></a>运行状况 
下面是“运行状况”功能的当前版本的已知问题：

- 如果删除了某个 Azure VM，该 VM 会在 VM 列表视图中显示一段时间。 此外，单击已删除 VM 的状态会打开“运行状况诊断”视图，然后启动加载循环。 选择已删除 VM 的名称会打开一个窗格，其中显示一条指出该 VM 已被删除的消息。
- 即使门户或工作负荷监视器 API 可以立即更新，但配置更改（例如更新阈值）最长需要 30 分钟才会生效。 
- 运行状况诊断体验的更新速度比其他视图更快。 在它们之间切换时，信息可能会延迟。 
- 对于 Linux VM，列出单个 VM 视图的运行状况条件的页标题包含 VM 的整个域名，而不是用户定义的 VM 名称。 
- 使用支持的方法之一对 VM 禁用监视功能后，再次尝试部署该 VM 时，应将它部署在同一个工作区中。 如果选择了另一个工作区并尝试查看该 VM 的运行状况，则它可能会显示不一致的行为。
- 从工作区中删除解决方案组件后，可以继续查看 Azure VM 的运行状况；具体而言，在门户中导航到任一视图时，可以查看性能和映射数据。 一段时间后，数据最终会在“性能”和“映射”视图中停止显示；但是，“运行状况”视图将继续显示 VM 的运行状况。 将提供“立即尝试”选项，用以仅从“性能”和“映射”视图中重新加入。

## <a name="next-steps"></a>后续步骤
若要了解启用虚拟机监视的要求和方法，请查看[Enable 用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。
