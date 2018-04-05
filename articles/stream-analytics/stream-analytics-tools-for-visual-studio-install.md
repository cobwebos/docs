---
title: 用于 Visual Studio 的 Azure 流分析工具的安装说明 | Microsoft 文档
description: 用于 Visual Studio 的 Azure 流分析工具的安装说明
keywords: visual studio
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: fe1f2158439b2c04e8232eb6fe3c9faf79641ffe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>用于 Visual Studio 的流分析工具的安装说明
Azure 流分析工具现在支持 Visual Studio 2017、2015 和 2013。 在本文档中，我们将介绍如何安装和卸载工具。

了解如何使用[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)。

## <a name="install"></a>安装
### <a name="visual-studio-2017"></a>Visual Studio 2017
* 下载 [Visual Studio 2017（15.3 或更高版本）](https://www.visualstudio.com/)。 支持 Enterprise (Ultimate/Premium)、Professional、Community 版本。 不支持 Express 版本。 
* 流分析工具是 Visual Studio 2017 中 **Azure 开发**以及**数据存储和处理**工作负载的一部分。 作为 VisualStudio 安装的一部分，启用这两个工作负载中的任何一个。

启用“数据存储和处理”工作负载，如下所示：

![“数据存储和处理”工作负载](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

启用“Azure 开发”工作负载，如下所示：

![“Azure 开发”工作负载](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
* 安装 Visual Studio 2015 或 Visual Studio 2013 Update 4。 支持 Enterprise (Ultimate/Premium)、Professional、Community 版本。 不支持 Express 版本。 
* 使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装用于 .NET 的 Microsoft Azure SDK 版本 2.7.1 或更高版本。
* 安装[适用于 Visual Studio 的 Azure 流分析工具](http://aka.ms/asatoolsvs)。



## <a name="update"></a>更新

### <a name="visual-studio-2017"></a>Visual Studio 2017
新的版本提醒显示在 Visual Studio 通知中。 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
已安装的适用于 Visual Studio 的流分析工具会自动检查是否有新版本。 按照弹出窗口中的说明安装最新版本。 


## <a name="uninstall"></a>卸载

### <a name="visual-studio-2017"></a>Visual Studio 2017
双击 Visual Studio 安装程序并选择“修改”。 从“数据存储和处理”工作负载或“Azure 开发”工作负载中取消选中“Azure Data Lake 和流分析工具”复选框。

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
转到控制面板，卸载“适用于 Visual Studio 的 Microsoft Azure Data Lake 和流分析工具”。





