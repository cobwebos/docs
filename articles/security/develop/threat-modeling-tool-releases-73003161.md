---
title: Microsoft Threat Modeling Tool 版本 03/22/2020-Azure
description: 阐述 Threat Modeling Tool 的发行说明
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146857"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新版本 7.3.00316.1-03/22/2020

Microsoft Threat Modeling Tool （TMT）的版本7.3.00316.1 于 22 2020 年3月发布，并且包含以下更改：

- 辅助功能改进
- Bug 修复
- 新的 DiagramReader 功能

## <a name="notable-bug-fixes"></a>值得注意的 bug 修复

### <a name="exporting-the-threat-list-to-csv"></a>将威胁列表导出到 CSV

导出到 CSV 函数的方式不一致选择要导出威胁列表中的哪些字段。 现在，威胁列表中的所有字段都将导出到 CSV 文件中。 

### <a name="ux-bugs"></a>UX bug

- 主工作流（创建/打开/分析）和模板编辑器体验中的 "帮助" 菜单现在具有一致的菜单选项。
- "模具" 窗格中的搜索栏现在具有标准光标并添加了相应的标签。

## <a name="new-features"></a>新增功能

### <a name="diagramreader-feature-has-been-added"></a>已添加 DiagramReader 功能

当模型处于打开状态时，在主菜单中添加了新的 DiagramReader 功能。 此功能会将模型的图形表示形式转换为基于文本的叙述。 

## <a name="system-requirements"></a>系统要求

- 支持的操作系统：
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 需要 .NET 版本：
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)或更高版本
- 附加要求：
  - 用于接收对工具和模板的更新的 internet 连接

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
