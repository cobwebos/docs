---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 1b650fa5a0e9ba2f7019e6e67690d9d1fd65e72a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894908"
---
安全中心为容器化环境提供实时威胁防护，并针对可疑活动生成警报。 可以使用此信息快速补救安全问题，并提高容器的安全性。

安全中心在不同的级别提供威胁防护： 

* **Azure defender 为服务器提供的主机级别 () ** -使用安全中心在其他 vm 上使用的同一个 Log Analytics 代理，Azure Defender 会监视你的 Linux AKS 节点中是否存在可疑活动，如 web shell 检测和与已知的可疑 IP 地址的连接。 此代理还监视容器特定的分析，如特权容器的创建、对 API 服务器的可疑访问以及安全外壳在 Docker 容器中运行 (SSH) 服务器。

    >[!IMPORTANT]
    > 如果你选择不在主机上安装代理，则只能收到一部分威胁防护权益和安全警报。 你仍会收到与网络分析以及与恶意服务器通信相关的警报。

    有关 AKS 主机级别警报的列表，请参阅警报的 [引用表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)。


* **Azure Defender 为 Kubernetes) 提供 AKS 群集级别 (** -在群集级别，威胁防护基于分析 Kubernetes 审核日志。 若要启用此 **无代理** 监视，请启用 Azure Defender。 为了在此级别生成警报，安全中心将使用 AKS 检索到的日志来监视 AKS 管理的服务。 此级别的事件示例包括公开 Kubernetes 仪表板、创建高特权角色，以及创建敏感的装入点。

    >[!NOTE]
    > 安全中心针对在订阅设置中启用“Kubernetes”选项后发生的 Azure Kubernetes 服务操作和部署生成安全警报。 

    有关 AKS 群集级别的警报列表，请参阅[警报参考表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)。

此外，我们的全球安全研究团队会不断监视威胁态势。 一旦发现威胁，他们就会添加容器特定的警报和漏洞。

> [!TIP]
> 可以按照[此博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的说明来模拟容器警报。