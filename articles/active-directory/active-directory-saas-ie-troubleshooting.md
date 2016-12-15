---
title: "Internet Explorer 访问面板扩展故障排除 | Microsoft Docs"
description: "如何使用组策略为我的应用门户部署 Internet Explorer 加载项。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43768d76e3cdc0dc34a3412675ea4fb84170c021


---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 访问面板扩展故障排除
本文将帮助用户解决以下问题：

* 使用 Internet Explorer 时无法通过我的应用门户访问应用。
* 即使已安装软件，也看到“安装软件”消息。

如果你是管理员，另请参阅[如何使用组策略部署 Internet Explorer 的访问面板扩展](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>运行诊断工具
可以下载并运行访问面板诊断工具，使用访问面板扩展来诊断安装问题：

1. [单击此处可下载诊断工具](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)。
2. 打开文件，然后按“全部提取”按钮。
   
    ![按“全部提取”](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. 然后按“提取”按钮以继续。
   
    ![按“提取”](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. 若要运行该工具，请右键单击名为“AccessPanelExtensionDiagnosticTool”的文件，然后选择“打开方式”>“基于 Microsoft Windows 的脚本主机”。
   
    ![打开方式 > 基于 Microsoft Windows 的脚本主机](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. 然后将看到以下诊断窗口，其中说明安装出现哪些信息可能是错误的。
   
    ![诊断窗口的一个示例](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. 单击“是”，让程序修复已发现的问题。
7. 若要保存这些更改，请关闭每个 Internet Explorer 窗口，然后重新打开 Internet Explorer。<br />如果仍然无法访问应用，请尝试执行以下步骤。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>检查访问面板扩展是否已启用
若要在 Internet Explorer 中验证访问面板扩展是否已启用，请执行以下操作：

1. 在 Internet Explorer 中，单击窗口右上角的“齿轮”图标。 然后选择“Internet 选项”。<br />（在较旧版本的 Internet Explorer 中可以在“工具”>“Internet 选项”下找到此项。）
   
    ![转到“工具”>“Internet 选项”](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. 单击“程序”选项卡，然后单击“管理加载项”按钮。
   
    ![单击“管理加载项”](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. 在此对话框中，选择“访问面板扩展”，然后单击“启用”按钮。
   
    ![单击“启用”](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. 若要保存这些更改，请关闭每个 Internet Explorer 窗口，然后重新打开 Internet Explorer。

## <a name="enable-extensions-for-inprivate-browsing"></a>为 InPrivate 浏览启用扩展
如果使用 InPrivate 浏览模式，请执行以下操作：

1. 在 Internet Explorer 中，单击窗口右上角的“齿轮”图标。 然后选择“Internet 选项”。<br />（在较旧版本的 Internet Explorer 中可以在“工具”>“Internet 选项”下找到此项。）
   
    ![诊断窗口的一个示例](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. 转到“隐私”选项卡，然后**取消选中**标记为“在 InPrivate 浏览启动时禁用工具栏和扩展”的复选框</p>
   
    ![取消选中“在 InPrivate 浏览启动时禁用工具栏和扩展”](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. 若要保存这些更改，请关闭每个 Internet Explorer 窗口，然后重新打开 Internet Explorer。

## <a name="uninstall-the-access-panel-extension"></a>卸载访问面板扩展
若要从计算机中卸载访问面板扩展，请执行以下操作：

1. 在键盘上按 **Windows 键**，打开“开始”菜单。 打开菜单后，可以键入任何内容以执行搜索。 键入“控制面板”，然后在“控制面板”出现在搜索结果中时打开**控制面板**。
   
    ![搜索“控制面板”](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. 在控制面板的右上角，将“查看方式”选项更改为“大图标”。 然后找到并单击“程序和功能”按钮。
   
    ![将视图更改为显示大图标](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. 从列表中选择“访问面板扩展”，然后单击“卸载”按钮。
   
    ![单击“卸载”](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. 然后，可以尝试重新安装扩展，以了解问题是否已解决。

如果在卸载扩展时遇到问题，还可以使用 [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) 工具删除它。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [Azure Active Directory 的应用程序访问与单一登录](active-directory-appssoaccess-whatis.md)
* [如何使用组策略部署 Internet Explorer 的访问面板扩展](active-directory-saas-ie-group-policy.md)




<!--HONumber=Nov16_HO3-->


