---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800592"
---
安全中心为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同的级别提供威胁防护： 

* **主机级别**-Log Analytics 代理监视 Linux 中的可疑活动。 该代理针对源自某个节点或其上运行的容器的可疑活动触发警报。 此类活动的示例包括使用已知可疑的 IP 地址进行 Web shell 检测和连接。

    为了更深入地洞察容器化环境的安全性，该代理会监视容器特定的分析结果。 它会针对创建特权容器、以可疑方式访问 API 服务器以及在 Docker 容器内部运行安全外壳 (SSH) 服务器等事件触发警报。

    >[!IMPORTANT]
    > 如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

    有关 AKS 主机级别警报的列表，请参阅警报的[引用表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)。


* 在 **AKS 群集级别**，威胁防护基于 Kubernetes 审核日志的分析。 若要启用此**无代理**监视功能，请在“定价和设置”页中将“Kubernetes”选项添加到订阅（请参阅[定价](https://docs.microsoft.com/azure/security-center/security-center-pricing)）。 为了在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    >[!NOTE]
    > 安全中心针对在订阅设置中启用“Kubernetes”选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

    有关 AKS 群集级别的警报列表，请参阅[警报参考表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。