---
title: 安装适用于 Visual Studio 的 Azure 流分析工具
description: 本文介绍安装要求以及如何安装适用于 Visual Studio 的 Azure 流分析工具。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安装适用于 Visual Studio 的 Azure 流分析工具
Azure 流分析工具现在支持 Visual Studio 2017、2015 和 2013。 本文档介绍如何安装和卸载工具。

有关如何使用工具的详细信息，请参阅[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)。

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





