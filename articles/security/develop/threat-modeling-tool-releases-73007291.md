---
title: Microsoft Threat Modeling Tool 版本 07/29/2020-Azure
description: 记录威胁建模工具 release 7.3.00729.1 的发行说明。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: fc343f292fa32ed2db67dd74aba7a66dbc00d6ab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317831"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 更新版本 7.3.00729.1-07/29/2020

29 2020 年7月发布的 Microsoft Threat Modeling Tool (TMT) 的版本7.3.00729.1 包含以下更改：

- Bug 修复
 
## <a name="known-issues"></a>已知问题

### <a name="errors-related-to-tmt7application-file-deserialization"></a>与 TMT7 相关的错误。

#### <a name="issue"></a>问题

某些客户在下载 Threat Modeling Tool 时报告接收到以下错误消息：

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

之所以发生此错误，是因为某些浏览器不支持 ClickOnce 安装。 在这些情况下，ClickOnce 应用程序文件将下载到用户的硬盘驱动器。

#### <a name="workaround"></a>解决方法

如果通过双击 TMT7 文件启动 Threat Modeling Tool，则会继续显示此错误。 但是，绕过错误后，该工具将正常运行。 用户应使用在安装过程中在 Windows 菜单中创建的快捷方式来启动 Threat Modeling Tool，而不是通过双击 TMT7 文件来启动 Threat Modeling Tool。

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[有关如何使用该工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。
