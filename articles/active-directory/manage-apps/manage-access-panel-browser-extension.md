---
title: 对 IE 的 Azure 访问面板扩展进行故障排除 |微软文档
description: 如何使用组策略为我的应用门户部署 Internet Explorer 加载项。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723916"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>排除 Internet 资源管理器的访问面板扩展故障

本文将帮助用户解决以下问题：

* 使用 Internet Explorer 时无法通过我的应用门户访问应用。
* 即使已安装软件，也看到“安装软件”消息。

如果您是管理员，请参阅[如何使用组策略部署 Internet 资源管理器的访问面板扩展](deploy-access-panel-browser-extension.md)。

## <a name="run-the-diagnostic-tool"></a>运行诊断工具

您可以通过下载并运行访问面板诊断工具来诊断"访问面板扩展"的安装问题。 

要下载并安装诊断工具：

1. [选择此链接可下载诊断工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 打开文件并将内容提取到计算机。
1. 要运行该工具，请右键单击名为*AccessPanel 扩展诊断工具.js*的文件，然后选择 **"使用** > **基于微软 Windows 的脚本主机**打开"。

    ![打开方式 > 基于 Microsoft Windows 的脚本主机](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 查看显示的诊断结果，然后选择 **"是**"以解决问题。 "**检查结果"** 对话框将显示有关扩展不起作用时应该怎么做的信息。  
1. 阅读邮件并选择 **"确定**"。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>检查访问面板扩展是否已启用

要验证您是否启用了 Internet 资源管理器中的访问面板扩展：

1. 在 Internet 资源管理器中，选择窗口右上角的**齿轮图标**，然后选择 Internet**选项**。
1. 转到"**程序**"选项卡并选择 **"管理加载项**"。
1. 在**Microsoft 公司**部分中选择 **"访问面板扩展****"，** 然后选择"启用"。
1. 要保存更改，关闭已打开的所有 Internet Explorer 浏览器窗口。 下次打开 Internet 资源管理器时，更改将生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>为 InPrivate 浏览启用扩展

要启用 InPrivate 浏览的扩展：

1. 在 Internet 资源管理器中，选择窗口右上角的**齿轮图标**，然后选择 Internet**选项**。
1. 转到 **"隐私"** 选项卡，并验证 **"在 InPrivate 浏览启动时禁用工具栏和扩展**"复选框是否清晰。
1. 要保存更改，关闭已打开的所有 Internet Explorer 浏览器窗口。 下次打开 Internet 资源管理器时，更改将生效。

## <a name="uninstall-the-access-panel-extension"></a>卸载访问面板扩展

要从计算机卸载访问面板扩展：

1. 在控制面板中，搜索*卸载*。
1. 在搜索结果中，选择 **"卸载程序**"。

    ![从"控制面板"中选择"卸载程序"选项](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 从列表中选择 **"访问面板扩展**"并选择 **"卸载**"。

    ![卸载访问面板扩展](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 然后，可以尝试重新安装扩展，以了解该问题是否已解决。

如果卸载扩展遇到问题，也可以使用[Microsoft 修复 It](https://go.microsoft.com/?linkid=9779673)工具将其删除。

## <a name="related-articles"></a>相关文章

* [Azure Active Directory 的应用程序访问与单一登录](what-is-single-sign-on.md)
* [如何使用组策略部署 Internet 资源管理器的访问面板扩展](deploy-access-panel-browser-extension.md)
