---
title: 适用于 IE 的 Azure 访问面板扩展故障排除 |Microsoft Docs
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67723916"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 的访问面板扩展疑难解答

本文将帮助用户解决以下问题：

* 使用 Internet Explorer 时无法通过我的应用门户访问应用。
* 即使已安装软件，也看到“安装软件”消息。

如果你是管理员，请参阅[如何使用组策略部署适用于 Internet Explorer 的访问面板扩展](deploy-access-panel-browser-extension.md)。

## <a name="run-the-diagnostic-tool"></a>运行诊断工具

可以通过下载并运行访问面板诊断工具来诊断访问面板扩展的安装问题。 

下载并安装诊断工具：

1. [选择此链接可下载诊断工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. 打开该文件并将内容提取到您的计算机。
1. 若要运行该工具，请右键单击名为 " *AccessPanelExtensionDiagnosticTool* " 的文件，然后选择 "**打开时** > **基于 Microsoft Windows 脚本宿主**"。

    ![打开方式 > 基于 Microsoft Windows 的脚本主机](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. 查看显示的诊断结果，并选择 **"是"** 以解决问题。 此时将显示 "**检查结果**" 对话框，其中包含有关扩展不起作用的信息。  
1. 阅读消息，然后选择 **"确定"**。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>检查访问面板扩展是否已启用

若要验证是否已启用 Internet Explorer 中的访问面板扩展，请执行以下操作：

1. 在 Internet Explorer 中，选择窗口右上角的**齿轮图标**，然后选择 " **Internet 选项**"。
1. 中转到 "**程序**" 选项卡并选择 "**管理加载项**"。
1. 在**Microsoft Corporation**部分选择 "**访问面板扩展**"，然后选择 "**启用**"。
1. 若要保存更改，请关闭已打开的所有 Internet Explorer 浏览器窗口。 更改将在下次打开 Internet Explorer 时生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>为 InPrivate 浏览启用扩展

若要为 InPrivate 浏览启用扩展，请执行以下操作：

1. 在 Internet Explorer 中，选择窗口右上角的**齿轮图标**，然后选择 " **Internet 选项**"。
1. 转到 "**隐私**" 选项卡，验证是否清除了 " **InPrivate 浏览启动时禁用工具栏和扩展**" 复选框。
1. 若要保存更改，请关闭已打开的所有 Internet Explorer 浏览器窗口。 更改将在下次打开 Internet Explorer 时生效。

## <a name="uninstall-the-access-panel-extension"></a>卸载访问面板扩展

若要从计算机中卸载访问面板扩展，请执行以下操作：

1. 在 "控制面板" 中，搜索 "*卸载*"。
1. 在搜索结果中，选择 "**卸载程序**"。

    ![从 "控制面板" 中选择 "卸载程序" 选项](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. 从列表中选择 "**访问面板扩展**"，然后选择 "**卸载**"。

    ![卸载访问面板扩展](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. 然后，可以尝试重新安装扩展，以了解该问题是否已解决。

如果在卸载扩展时遇到问题，还可以使用[Microsoft Fix it](https://go.microsoft.com/?linkid=9779673)工具将其删除。

## <a name="related-articles"></a>相关文章

* [Azure Active Directory 的应用程序访问与单一登录](what-is-single-sign-on.md)
* [如何使用组策略部署 Internet Explorer 的访问面板扩展](deploy-access-panel-browser-extension.md)
