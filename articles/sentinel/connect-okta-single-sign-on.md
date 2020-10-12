---
title: 将 Okta Single Sign-On data 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Okta Single Sign-On data 连接到 Azure Sentinel。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 05a9b8009d896a2ee87df3e1c4493d249a887566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083916"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>将 Okta Single Sign-On 连接到 Azure Sentinel with Azure Function

> [!IMPORTANT]
> Azure Sentinel 中的 Okta Single Sign-On 数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Okta Single Sign-On (SSO) 连接器可让你轻松地将所有 [Okta 单一 Sign-On (sso) ](https://www.okta.com/products/single-sign-on/) security 解决方案日志与 Azure Sentinel 连接，查看仪表板，创建自定义警报，并改善调查。 Okta Single Sign-On 与 Azure Sentinel 之间的集成利用 Azure Functions 通过 REST API 请求日志数据。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-okta-single-sign-on"></a>配置并连接 Okta 单一 Sign-On

Azure Functions 可以直接从 Okta 单个 Sign-On 集成并请求事件和日志，并将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，并选择 " **Okta 单一登录** 连接器"。

1. 选择 " **打开连接器" 页面**。

1. 按照 " **Okta 单一登录** " 页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **Okta_CL** " 表下 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Function App 将 Okta Single Sign-On 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

