---
title: 威胁检测的 Azure 安全中心中的 Azure 服务层 |Microsoft Docs
description: 本主题介绍了 Azure 安全中心中可用的 Azure 服务层警报。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571720"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>在 Azure 安全中心的 Azure 服务层的威胁检测

本主题介绍了安全中心警报可监视以下 Azure 服务层时。

* [Azure 网络层](#network-layer)
* [Azure 的管理层 （Azure 资源管理器） （预览版）](#management-layer)

>[!NOTE]
>使用安全中心将利用从利用 Azure 的内部源的遥测数据，下面提供了 analytics 都适用于所有资源类型。

## Azure 网络层<a name="network-layer"></a>

安全中心网络层分析基于样本[IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，这是数据包标头收集的 Azure 核心路由器。 根据此数据馈送，安全中心机器学习模型标识并将标记恶意流量的活动。 来丰富 IP 地址，安全中心将利用 Microsoft 的威胁智能数据库。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**可疑传出 RDP 网络活动**|采样的网络流量分析检测到异常传出远程桌面协议 (RDP) 通信源自您的部署中的资源。 此活动被视为异常为此环境，并可能表示你的资源已遭到破坏，正在正在用它来暴力破解外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**可疑传出 RDP 网络活动到多个目标**|采样的网络流量分析检测到到多个目标源自于您的部署中的资源的异常传出远程桌面协议 (RDP) 通信。 此活动被视为异常为此环境，并可能表示你的资源已遭到破坏，现在正在使用暴力破解外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**可疑传出 SSH 网络活动**|采样的网络流量分析检测到异常传出安全外壳 (SSH) 通信源自您的部署中的资源。 此活动被视为异常为此环境，并可能表示你的资源已遭到破坏，正在正在用它来暴力破解外部 SSH 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**可疑传出 SSH 网络活动到多个目标**|采样的网络流量分析检测到到多个目标源自于您的部署中的资源的异常传出安全外壳 (SSH) 通信。 此活动被视为异常为此环境，并可能表示你的资源已遭到破坏，现在正在使用暴力破解外部 SSH 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**来自多个源可疑传入 SSH 网络活动**|采样的网络流量分析你的部署中检测到异常传入 SSH 通信来自多个源到的资源。 连接到所需的资源的各个唯一 Ip 被视为异常为此环境。 此活动可能表明尝试暴力破解 SSH 接口从多个主机 （僵尸网络）。|
|**可疑的传入 SSH 网络活动**|采样的网络流量分析检测到在部署中的资源的异常传入 SSH 通信。 相对大量的传入连接到所需的资源被视为异常为此环境。 此活动可能表明尝试暴力破解 SSH 接口。
|**来自多个源可疑传入 RDP 网络活动**|采样的网络流量分析你的部署中检测到异常传入 RDP 通信来自多个源到的资源。 连接到所需的资源的各个唯一 Ip 被视为异常为此环境。 此活动可能表明暴力破解尝试 RDP 接口从多个主机 （僵尸网络）。|
|**可疑的传入 RDP 网络活动**|采样的网络流量分析检测到在部署中的异常对资源的传入 RDP 通信。 相对大量的传入连接到所需的资源被视为异常为此环境。 此活动可能表明尝试暴力破解 SSH 接口。|

若要了解安全中心可如何使用网络相关的信号来适用威胁防护，请参阅[Azure 安全中心中的启发式 DNS 检测](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。
## Azure 的管理层 （Azure 资源管理器） （预览版）<a name ="management-layer"></a>

>[!NOTE]
>安全中心保护层基于 Azure 资源管理器当前处于预览状态。

安全中心通过利用 Azure 资源管理器事件，它被视为是适用于 Azure 的控制平面提供附加保护层。 通过分析 Azure 资源管理器记录，安全中心检测到不寻常的或可能有害的操作在 Azure 订阅环境中。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**运行 microBurst 工具包**|运行一个已知的云环境的侦测工具包中检测到你的环境。 工具"MicroBurst"(请参阅 https://github.com/NetSPI/MicroBurst) 可由攻击者 （或渗透测试人员） 来映射你订阅的资源、 识别不安全的配置，并会泄漏机密信息。|
|**运行 azurite 工具包**|运行一个已知的云环境的侦测工具包中检测到你的环境。 工具"Azurite"(请参阅 https://github.com/mwrlabs/Azurite) 可由攻击者 （或渗透测试人员） 来映射你订阅的资源并确定不安全的配置。|
|**可疑的管理会话使用的非活动帐户**|订阅活动日志分析检测到可疑行为。 用于长时间，不是主体现在正在执行操作可用于保护对攻击者执行暂留。|
|**使用 PowerShell 的可疑的管理会话**|订阅活动日志分析检测到可疑行为。 主体不会定期使用 PowerShell 来管理订阅环境中，现在使用 PowerShell，并执行操作可用于保护对攻击者执行暂留。|
|**使用 Azure 的高级的持久性技术**|订阅活动日志分析检测到可疑行为。 自定义的角色有权 legitimized 的标识实体。 这可能会导致攻击者获得 Azure 客户环境中的持久性。|
|**来自不常见国家/地区的活动**|发生的活动从没有最近或从未访问过的组织中的任何用户的位置。<br/>此项检测考虑过去的活动位置，以确定新的和不常见的位置。 异常情况检测引擎将存储组织中用户以往用过的位置的相关信息。 
|**来自匿名 IP 地址的活动**|从已被标识为匿名代理 IP 地址已检测到的 IP 地址的用户活动。 <br/>这些代理通常是想要隐藏其设备 IP 地址的用户才会使用，他们可能意图不良。 此项检测利用的机器学习算法可以减少“误报”，例如，错误地标记组织中用户广泛使用的 IP 地址。|
|**检测到无法实现的行程**|两个用户活动 （是一个或多个会话） 发生了，源自地理位置内的时间段的时间短于它所需的用户从第一个位置前往第二个。 这表示不同的用户使用相同的凭据。 <br/>此检测利用机器学习算法，会忽略明显"误报"不可能前往条件，例如 Vpn 和组织中其他用户定期使用的位置。 此项检测的初始学习期限为 7 天，在此期间，它会学习新用户的活动模式。|

>[!NOTE]
> 多个以上的分析提供支持的 Microsoft 云应用安全 (MCAS)。 若要从这些分析中受益，激活的 MCAS 许可证是必需的。 如果你拥有 MCAS 许可证，则默认情况下会启用这些警报。 若要禁用它们：
>
> 1. 在“安全中心”边栏选项卡中，选择“安全策略”。  针对要更改的订阅，单击“编辑设置”。 
> 2. 单击“威胁检测”。 
> 3. 在“启用集成”下，取消选中“允许 Microsoft Cloud App Security 访问我的数据”，然后单击“保存”。   

>[!NOTE]
>Azure 安全中心将与安全相关的客户数据存储在其资源所在的同一区域。 如果 Microsoft 具有尚未部署 Azure 安全中心中资源的异地，然后它将数据存储在美国。 启用 Microsoft 云应用安全 (MCAS) 后，按照 MCAS 的地理位置规则存储此信息。 请参阅[有关详细信息的非区域性服务的数据存储](http://azuredatacentermap.azurewebsites.net/)。
