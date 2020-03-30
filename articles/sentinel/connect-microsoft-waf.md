---
title: 将 Web 应用程序防火墙数据连接到 Azure 哨兵
description: 了解如何将 Microsoft Web 应用程序防火墙数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588172"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>连接来自微软 Web 应用程序防火墙的数据



可以从 Azure 应用程序网关的 Microsoft Web 应用程序防火墙 （WAF） 流式传输日志。 此 WAF 可保护应用程序免受常见的 Web 漏洞（如 SQL 注入和跨站点脚本）的影响，并允许您自定义规则以减少误报。 按照这些说明将 Microsoft Web 应用程序防火墙日志流式传输到 Azure Sentinel 中。


## <a name="prerequisites"></a>先决条件

- 现有应用程序网关资源

## <a name="connect-to-microsoft-web-application-firewall"></a>连接到微软 Web 应用程序防火墙

如果您已经拥有 Microsoft Web 应用程序防火墙，请确保拥有现有的网关资源。
部署 Microsoft Web 应用程序防火墙并获取数据后，警报数据可以轻松地流式传输到 Azure Sentinel 中。
    
1. 在 Azure Sentinel 门户中，选择 **"数据连接器**"。
1. 在"数据连接器"页中，选择**WAF**磁贴。
1. 转到[应用程序网关资源](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) 并选择您的 WAF。
    1. 选择“诊断设置”****。
    1. 选择 = 在表下**添加诊断设置**。
    1. 在 **"诊断设置"** 页中，键入 **"名称**"并选择"**发送到日志分析**"。
    1. 在**日志分析工作区**下选择 Azure 哨兵工作区。
    1. 选择要分析的日志类型。 我们建议：应用程序网关访问日志和应用程序网关防火墙日志。
1. 要在日志分析中为 Microsoft Web 应用程序防火墙警报使用相关架构，请搜索**Azure 诊断**。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Microsoft Web 应用程序防火墙连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
