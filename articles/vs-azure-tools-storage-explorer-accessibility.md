---
title: Azure 存储资源管理器可访问性 | Microsoft Docs
description: 了解 Azure 存储资源管理器中的可访问性。 查看可用的读屏器、缩放功能、高对比度主题和快捷键。
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035479"
---
# <a name="storage-explorer-accessibility"></a>存储资源管理器可访问性

## <a name="screen-readers"></a>屏幕阅读器

存储资源管理器支持在 Windows 和 Mac 中使用屏幕阅读器。 建议对每种平台使用以下屏幕阅读器：

平台 | 屏幕阅读器
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | （Linux 不支持屏幕阅读器）

如果使用存储资源管理器时遇到可访问性问题，请[在 GitHub 上提交问题](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="zoom"></a>Zoom

可通过放大操作，将存储资源管理器中的文本放大。 若要放大，请在“帮助”菜单中单击“放大”****。 还可使用“帮助”菜单缩小文本和将缩放级别重置回默认级别。

![帮助菜单中的缩放选项][0]

缩放设置增加大多数 UI 元素的大小。 建议同时为 OS 启用大型文字和缩放设置以确保所有 UI 元素都正确缩放。

## <a name="high-contrast-themes"></a>高对比度主题

存储资源管理器具有两个高对比度主题，“高对比度浅色”和“高对比度深色”********。 可以通过从“帮助”>“主题”菜单中进行选择来更改主题。

![“主题”子菜单][1]

主题设置更改大多数 UI 元素的颜色。 建议同时启用 OS 的匹配的高对比度主题，以确保所有 UI 元素都正确着色。

## <a name="shortcut-keys"></a>快捷键

### <a name="window-commands"></a>窗口命令

命令       | 键盘快捷键
--------------|--------------------
新建窗口    | **Control+Shift+N**
关闭编辑器  | **Control+F4**
Quit          | **Control+Shift+W**

### <a name="navigation-commands"></a>导航命令

命令                | 键盘快捷键
-----------------------|----------------------
聚焦于下一个面板       | **F6**
聚焦于上一个面板   | **Shift+F6**
资源管理器               | **Control+Shift+E**
帐户管理     | **Control+Shift+A**
切换侧边栏        | **Control+B**
活动日志           | **Control+Shift+L**
操作和属性 | **Control+Shift+P**
当前编辑器         | **Control+Home**
下一个编辑器            | **Control+Page Down**
上一个编辑器        | **Control+Page Up**

### <a name="zoom-commands"></a>缩放命令

命令  | 键盘快捷键
---------|------------------
放大  | **Control+=**
缩小 | **Control+-**

### <a name="blob-and-file-share-editor-commands"></a>Blob 和文件共享编辑器命令

命令 | 键盘快捷键
--------|--------------------
返回    | **Alt+向左键**
前进 | **Alt+向右键**
向上      | **Alt+向上键**

### <a name="editor-commands"></a>编辑器命令

命令 | 键盘快捷键
--------|------------------
复制    | **Ctrl+C**
剪切     | **Ctrl+X**
粘贴   | **Ctrl+V**
刷新  | **Control+R**

### <a name="other-commands"></a>其他命令

命令                | 键盘快捷键
-----------------------|------------------
切换开发人员工具 | **F12**
重新加载                 | **Alt+Control+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
