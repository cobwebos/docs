---
title: 部署代理以将 CEF 数据连接到 Azure 哨兵预览*微软文档
description: 了解如何部署代理以将 CEF 数据连接到 Azure Sentinel。
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588461"
---
# <a name="step-1-deploy-the-agent"></a>第 1 步：部署代理


在此步骤中，您需要选择在 Azure Sentinel 和安全解决方案之间充当代理的 Linux 计算机。 您必须在代理计算机上运行脚本：
- 安装日志分析代理并根据需要对其进行配置，以侦听 Syslog 消息。
- 配置 Syslog 守护进程以使用 TCP 端口 514 侦听 Syslog 消息，然后仅使用 TCP 端口 25226 将 CEF 消息转发到日志分析代理。
- 设置 Syslog 代理以收集数据并将其安全地发送到 Azure Sentinel，并对其进行分析和丰富。
 
## <a name="deploy-the-agent"></a>部署代理
 
1. 在 Azure Sentinel 门户中，单击 **"数据"连接符**并选择 **"常见事件格式 "（CEF），** 然后**选择"打开连接器"页**。 

1. 在 **"安装和配置 Syslog 代理**"下，选择计算机类型（Azure、其他云或本地）。 
   > [!NOTE]
   > 由于下一步的脚本安装日志分析代理并将计算机连接到 Azure Sentinel 工作区，因此请确保此计算机未连接到任何其他工作区。
1. 您的计算机上必须具有提升的权限 （sudo）。 请确保计算机上使用以下命令具有 Python：`python –version`

1. 在代理计算机上运行以下脚本。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 脚本运行时，请检查以确保不会收到任何错误或警告消息。

继续[执行步骤 2：配置安全解决方案以转发 CEF 消息](connect-cef-solution-config.md)。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。

