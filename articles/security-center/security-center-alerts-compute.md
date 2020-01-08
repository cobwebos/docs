---
title: Azure 安全中心内云本机计算的威胁检测 |Microsoft Docs
description: 本文介绍 Azure 安全中心提供的云本机计算警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: f2fda893ad84aaf9d11d26d761f5395c7f5650d2
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666366"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure 安全中心内云本机计算的威胁检测

作为本机解决方案，Azure 安全中心对于跨多个目标的攻击方法识别的内部日志具有独特的可见性。 本文介绍适用于以下 Azure 服务的警报：

* [Azure 应用服务](#app-services)
* [Azure 容器](#azure-containers) 

> [!NOTE]
> 这些服务当前在 Azure 政府版和主权云区域中不可用。

## Azure App Service<a name="app-services"></a>

安全中心使用云的规模来确定针对应用服务上运行的应用程序的攻击。 攻击者探查 web 应用程序以找出并利用弱点。 在将路由到特定环境之前，对在 Azure 中运行的应用程序的请求会经历多个网关，并在其中进行检查和记录。 然后，将使用此数据来识别攻击者和攻击者，并了解稍后将使用的新模式。

通过使用 Azure 作为云提供商的可见性，安全中心将分析应用服务内部日志，以确定多个目标的攻击方法。 例如，方法包含广泛的扫描和分布式攻击。 这种类型的攻击通常来自一小部分 Ip，并显示对多个主机上的类似终结点进行爬网的模式。 攻击会搜索易受攻击的页面或插件，不能从单个主机的角度来识别。

安全中心还可以访问基础沙箱和 Vm。 与内存取证一起，基础结构可以从一种新的攻击传播到客户计算机中的威胁，从而讲述这一案例。 因此，即使在 web 应用被利用后部署了安全中心，它也能够检测到正在进行的攻击。

有关 Azure App Service 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureappserv)。

## Azure 容器<a name="azure-containers"></a>

安全中心为容器化环境提供实时威胁检测，并生成可疑活动的警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

我们检测到不同级别的威胁： 

* **主机级别**-安全中心的代理（在标准层上提供，有关详细信息，请参阅[定价](security-center-pricing.md)）监视 Linux 中的可疑活动。 对于源自节点或在其上运行的容器的可疑活动，代理会触发警报。 此类活动的示例包括 web shell 检测和与已知的可疑 IP 地址的连接。

    为了更深入地了解容器化环境的安全性，代理会监视容器特定的分析。 它将触发对在 Docker 容器中运行的事件（例如特权容器创建、可疑访问 API 服务器和安全外壳（SSH）服务器）的警报。

    >[!NOTE]
    > 如果选择不在主机上安装代理，则只会收到威胁检测权益和警报的子集。 你仍将收到与网络分析和恶意服务器通信相关的警报。

    有关主机级别警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-containerhost)。


* 对于**AKS 群集级别**，会根据 Kubernetes 审核日志分析来监视威胁检测。 若要启用此**无代理**监视，请从 **& 设置**"页的" 定价 "中将" Kubernetes "选项添加到你的订阅（请参阅[定价](security-center-pricing.md)）。 若要在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开的 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装载。

    >[!NOTE]
    > 安全中心为在订阅设置上启用 Kubernetes 选项后发生的 Azure Kubernetes 服务操作和部署生成检测警报。 

    有关 AKS 群集级别警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-akscluster)。

另外，我们的安全研究人员的全球安全研究人员不断监视威胁的发展。 它们在发现时添加容器特定的警报和漏洞。