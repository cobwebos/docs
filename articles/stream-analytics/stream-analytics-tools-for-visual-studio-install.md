---
title: 安装适用于 Visual Studio 的 Azure 流分析工具
description: 本文介绍安装要求，以及如何设置适用于 Visual Studio 的 Azure 流分析工具。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 447fa07953c15fe67b8a2e313fe9534164f47bbd
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130505"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安装适用于 Visual Studio 的 Azure 流分析工具

Visual Studio 2019 和 Visual Studio 2017 支持 Azure Data Lake 和流分析工具。 本文介绍如何安装和卸载工具。

有关使用这些工具的详细信息，请[参阅快速入门：使用 Visual Studio](stream-analytics-quick-create-vs.md)创建 Azure 流分析作业。

## <a name="install"></a>安装

Visual Studio Enterprise （旗舰版/高级版）、专业版和社区版支持这些工具。 Express edition 和 Visual Studio for Mac 不支持它们。

建议 Visual Studio 2019。

### 安装 Visual Studio 2019 和2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake 和流分析工具是**Azure 开发**以及**数据存储和处理**工作负荷的一部分。 请在安装过程中启用这两个工作负荷中的一个。 如果已安装 Visual Studio，请选择 "**工具** > " "**获取工具和功能**" 来添加工作负荷。

下载[Visual studio 2019 （预览版2或更高版本）或 Visual studio 2017 （15.3 或更高版本）](https://www.visualstudio.com/) ，然后按照说明进行安装。

选择**数据存储和处理**工作负荷，如下所示：

![选择“数据存储和处理”工作负载](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

选择**Azure 开发**工作负载，如下所示：

![选择“Azure 开发”工作负载](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

添加工作负荷后，请更新这些工具。 此过程引用 Visual Studio 2019：

1. 选择**扩展** > **管理扩展**。

1. 在 "**管理扩展**" 中，选择 "**更新**" 并选择 " **Azure Data Lake 和流分析工具**"。

1. 选择 "**更新**" 以安装最新扩展。

![Visual Studio 扩展和更新](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### 安装 Visual Studio 2015 和2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise （旗舰版/高级版）、专业版和社区版支持这些工具。 Express edition 不支持它们。

* 安装 Visual Studio 2015 或 Visual Studio 2013 Update 4。
* 使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)安装用于 .NET 的 Microsoft Azure SDK 版本 2.7.1 或更高版本。
* 安装[Microsoft Azure Data Lake 和适用于 Visual Studio 的流分析工具](https://www.microsoft.com/en-us/download/details.aspx?id=49504)。

## 时更新<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

对于 Visual Studio 2019 和 Visual Studio 2017，新版本提醒显示为 Visual Studio 通知。

对于 Visual Studio 2015 和 Visual Studio 2013，工具会自动检查是否有新版本。 按照说明安装最新版本。

## <a name="uninstall"></a>卸载

你可以卸载 Azure Data Lake 和流分析工具。 对于 visual studio 2019 或 visual studio 2017，请选择 "**工具** > " "**获取工具和功能**"。 在**修改**中，取消选择**Azure Data Lake 和流分析工具**。 它显示在**数据存储和处理**工作负荷或**Azure 开发**工作负载下。

若要从 Visual Studio 2015 或 Visual Studio 2013 中卸载，请参阅 **"控制面板" "**  > **程序和功能**"。 卸载**Microsoft Azure Data Lake 和适用于 Visual Studio 的流分析工具**。
