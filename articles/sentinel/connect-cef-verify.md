---
title: 验证与 Azure 哨兵的连接*微软文档
description: 验证安全解决方案的连接性，以确保 CEF 消息被转发到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588427"
---
# <a name="step-3-validate-connectivity"></a>第 3 步：验证连接



部署代理并将安全解决方案配置为转发 CEF 消息后，请使用本文了解如何验证 Azure Sentinel 和安全解决方案之间的连接。 

## <a name="how-to-validate-connectivity"></a>如何验证连接

1. 打开日志分析，以确保使用通用安全日志架构接收日志。<br> 可能需要 20 分钟以上，直到日志开始出现在日志分析中。 

1. 在运行脚本之前，我们建议您从安全解决方案发送消息，以确保邮件被转发到您配置的 Syslog 代理计算机。 
1. 您的计算机上必须具有提升的权限 （sudo）。 请确保计算机上使用以下命令具有 Python：`python –version`
1. 运行以下脚本以检查代理、Azure Sentinel 和安全解决方案之间的连接。 它检查守护进程转发是否配置正确，侦听正确的端口，并且没有任何阻止守护进程和日志分析代理之间的通信。 该脚本还会发送模拟消息"TestCommonEventFormat"以检查端到端连接。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

