---
title: 将 AI Vectra 检测数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 AI Vectra 检测数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038216"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>将 AI Vectra 检测连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 AI Vectra 检测数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何将 [AI Vectra 检测](https://www.vectra.ai/product/cognito-detect) 设备连接到 Azure Sentinel。 AI Vectra 检测数据连接器可让你轻松地将 AI Vectra 检测到 Azure Sentinel，使你能够在工作簿中查看数据，使用它创建自定义警报，并将其合并以改进调查。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>配置 AI Vectra 检测设备发送 CEF 消息  

配置 AI Vectra 检测，通过 [步骤1：部署日志转发器](connect-cef-agent.md)中设置的 Syslog 转发器，将 CEF 格式的 syslog 消息转发到 Log Analytics 工作区。

1. 在 Vectra 接口中，导航到 "设置" > 通知 "，然后选择" 编辑 Syslog 配置 "。 按照以下说明设置连接：

    - 将新目标添加 ([日志转发器](connect-cef-agent.md) 的主机名) 
    - 将端口设置为 **514**
    - 将协议设置为 **UDP**
    - 将格式设置为 **CEF**
    - 设置日志类型 (选择所有可用的日志类型) 
    - 单击“保存”

2. 您可以单击 " **测试** " 按钮来强制将一些测试事件发送到日志转发器。

3. 若要在 AI Vectra 检测事件的 Log Analytics 中使用相关架构，请搜索 **CommonSecurityLog**。

4. 继续执行[步骤 3：验证连接性](connect-cef-verify.md)。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 AI Vectra 检测装置连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击 "数据连接器" 页上的 " **后续步骤** " 选项卡。 您可以在其中找到一些现成的示例查询，以便您可以开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
