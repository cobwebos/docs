---
title: 获得为 IE Azure 访问面板扩展进行故障排除 |Microsoft Docs
description: 如何使用组策略为我的应用门户部署 Internet Explorer 加载项。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291517"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Internet explorer 访问面板扩展进行故障排除

本文将帮助用户解决以下问题：

* 使用 Internet Explorer 时无法通过我的应用门户访问应用。
* 即使已安装软件，也看到“安装软件”消息。

如果你是管理员，请参阅[如何部署使用组策略的 Internet Explorer 访问面板扩展](deploy-access-panel-browser-extension.md)。

## <a name="run-the-diagnostic-tool"></a>运行诊断工具

可以通过下载并运行访问面板诊断工具来诊断安装问题的访问面板扩展。 

若要下载并安装诊断工具：

1. [选择此链接以下载该诊断工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. 打开文件并将内容提取到您的计算机。
   
3. 若要运行该工具，用鼠标右键单击名为的文件*AccessPanelExtensionDiagnosticTool.js* ，然后选择**使用打开** > **Microsoft Windows 基于脚本宿主**.
   
    ![打开方式 > 基于 Microsoft Windows 的脚本主机](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. 查看诊断结果的显示并选择**是**来解决问题。 **检查结果**对话框显示有关要执行的操作如果扩展不起作用的信息。  

5. 读取消息并选择**确定**。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>检查访问面板扩展是否已启用

若要验证已启用 Internet Explorer 中的访问面板扩展：

1. 在 Internet Explorer 中，选择**齿轮图标**右上角的窗口，然后选择**Internet 选项**。
   
2. 转到**程序**选项卡并选择**管理加载项**。
   
3. 选择**访问面板扩展**中**Microsoft Corporation**部分，并选择**启用**。
   
4. 若要保存所做的更改，关闭所有 Internet Explorer 浏览器窗口必须打开。 更改下次打开 Internet Explorer 时生效。

## <a name="enable-extensions-for-inprivate-browsing"></a>为 InPrivate 浏览启用扩展

为 InPrivate 浏览启用扩展：

1. 在 Internet Explorer 中，选择**齿轮图标**右上角的窗口，然后选择**Internet 选项**。
   
2. 转到**隐私**选项卡，确认**InPrivate 浏览启动时禁用工具栏和扩展**复选框已清除。
   
3.  若要保存所做的更改，关闭所有 Internet Explorer 浏览器窗口必须打开。 更改下次打开 Internet Explorer 时生效。

## <a name="uninstall-the-access-panel-extension"></a>卸载访问面板扩展

若要从计算机中卸载访问面板扩展：

1. 在控制面板中，搜索*卸载*。 

2. 在搜索结果中，选择**卸载程序**。
   
    ![卸载程序的搜索。](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. 从列表中选择**访问面板扩展**，然后选择**卸载**。

    ![卸载访问面板扩展。](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. 然后，可以尝试重新安装扩展，以了解该问题是否已解决。

如果遇到问题卸载扩展时，您还可以删除它使用[Microsoft Fix It](https://go.microsoft.com/?linkid=9779673)工具。

## <a name="related-articles"></a>相关文章
* [Azure Active Directory 的应用程序访问与单一登录](what-is-single-sign-on.md)
* [如何部署使用组策略的 Internet Explorer 访问面板扩展](deploy-access-panel-browser-extension.md)

