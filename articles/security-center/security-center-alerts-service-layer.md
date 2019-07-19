---
title: Azure 安全中心内 Azure 服务层的威胁检测 |Microsoft Docs
description: 本主题介绍 Azure 安全中心提供的 Azure 服务层警报。
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
ms.author: v-mohabe
ms.openlocfilehash: f795822d76def4a6695a4746fba7e8566041cb2b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295554"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Azure 安全中心中 Azure 服务层的威胁检测

本主题提供监视以下 Azure 服务层时可用的安全中心警报。

* [Azure 网络层](#network-layer)
* [Azure 管理层 (Azure 资源管理器) (预览版)](#management-layer)

>[!NOTE]
>通过使用安全中心利用的遥测数据来利用 Azure 内部源, 以下提供的分析适用于所有资源类型。

## Azure 网络层<a name="network-layer"></a>

安全中心网络层分析基于示例[IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), 这些数据是由 Azure 核心路由器收集的数据包标头。 基于此数据馈送, 安全中心机器学习模型识别并标记恶意流量活动。 为了浓缩 IP 地址, 安全中心利用了 Microsoft 的威胁情报数据库。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**可疑的传出 RDP 网络活动**|采样网络流量分析检测到从你的部署中的资源发起的异常传出远程桌面协议 (RDP) 通信。 此环境中的此活动被视为不正常, 这可能表示资源已遭到破坏, 并且现在正被用于暴力破解外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**到多个目标的可疑传出 RDP 网络活动**|采样网络流量分析检测到从部署中的资源到多个目标的异常传出远程桌面协议 (RDP) 通信。 此环境中的此活动被视为异常, 这可能表示你的资源已遭到破坏, 并且现在正被用于强力强制外部 RDP 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**可疑的传出 SSH 网络活动**|采样的网络流量分析检测到从部署中的资源发起的异常传出安全外壳 (SSH) 通信。 此环境中的此活动被视为不正常, 这可能表示资源已遭到破坏, 并且现在正被用于暴力破解外部 SSH 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**到多个目标的可疑传出 SSH 网络活动**|采样网络流量分析检测到从部署中的资源到多个目标的异常传出安全外壳 (SSH) 通信。 此环境中的此活动被视为不正常, 这可能表示你的资源已遭到破坏, 并且现在正在用于强力强制外部 SSH 终结点。 请注意，此类活动可能导致你的 IP 被外部实体标记为恶意 IP。|
|**来自多个源的可疑传入 SSH 网络活动**|采样网络流量分析检测到从多个源到部署中资源的异常传入 SSH 通信。 在此环境下, 连接到资源的不同唯一 Ip 被视为异常。 此活动可能表示尝试从多个主机 (僵尸网络) 暴力破解 SSH 接口。|
|**可疑的传入 SSH 网络活动**|采样网络流量分析检测到与部署中的资源的异常传入 SSH 通信。 在此环境中, 与资源相对较多的传入连接被视为不正常。 此活动可能表示尝试暴力破解 SSH 接口。
|**来自多个源的可疑传入 RDP 网络活动**|采样网络流量分析检测到从多个源到部署中资源的异常传入 RDP 通信。 在此环境下, 连接到资源的不同唯一 Ip 被视为异常。 此活动可能表示尝试从多个主机 (僵尸网络) 暴力破解 RDP 接口。|
|**可疑的传入 RDP 网络活动**|采样网络流量分析检测到与部署中的资源的异常传入 RDP 通信。 在此环境中, 与资源相对较多的传入连接被视为不正常。 此活动可能表示尝试暴力破解 SSH 接口。|

若要了解安全中心如何使用网络相关信号来应用威胁防护, 请参阅[在 Azure 安全中心中检测启发式 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。
## Azure 管理层 (Azure 资源管理器) (预览版)<a name ="management-layer"></a>

>[!NOTE]
>基于 Azure 资源管理器的安全中心保护层目前处于预览阶段。

安全中心通过利用 Azure 资源管理器事件 (被视为 Azure 的控制平面) 提供额外的保护层。 通过分析 Azure 资源管理器记录, 安全中心检测到 Azure 订阅环境中的异常或潜在有害操作。

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**MicroBurst 工具包运行**|已在你的环境中检测到一个已知的云环境侦测工具包。 工具 "MicroBurst" (请参阅 https://github.com/NetSPI/MicroBurst) 可由攻击者 (或渗透测试人员) 用来映射你的订阅资源、标识不安全的配置以及泄露机密信息。|
|**Azurite 工具包运行**|已在你的环境中检测到一个已知的云环境侦测工具包。 工具 "Azurite" (请参阅 https://github.com/mwrlabs/Azurite) 可由攻击者 (或渗透测试人员) 用来映射你的订阅资源并标识不安全的配置。|
|**使用非活动帐户的可疑管理会话**|订阅活动日志分析检测到可疑行为。 一段时间内未使用的主体现在正在执行可确保攻击者持久性的操作。|
|**使用 PowerShell 的可疑管理会话**|订阅活动日志分析检测到可疑行为。 不经常使用 PowerShell 管理订阅环境的主体现在正在使用 PowerShell, 并执行可确保攻击者持久性的操作。|
|**使用高级 Azure 持久性技术**|订阅活动日志分析检测到可疑行为。 自定义角色已被 legitimized 标识实体提供。 这可能会使攻击者在 Azure 客户环境中获得持久性。|
|**来自不常见国家/地区的活动**|组织中的任何用户最近或从未访问过的位置中的活动。<br/>此项检测考虑过去的活动位置，以确定新的和不常见的位置。 异常情况检测引擎将存储组织中用户以往用过的位置的相关信息。 
|**来自匿名 IP 地址的活动**|检测到来自已被标识为匿名代理 IP 地址的 IP 地址的用户活动。 <br/>这些代理通常是想要隐藏其设备 IP 地址的用户才会使用，他们可能意图不良。 此项检测利用的机器学习算法可以减少“误报”，例如，错误地标记组织中用户广泛使用的 IP 地址。|
|**检测到无法旅行**|发生了两个用户活动 (是单个或多个会话), 这些活动源自某个时间段内的地理位置遥远的时间段, 此时间段比用户从第一个位置到达第二个位置所需的时间短。 这表示其他用户正在使用相同的凭据。 <br/>此检测利用机器学习算法, 该算法忽略对不可能旅行情况 (如组织中其他用户定期使用的 Vpn 和位置) 造成的明显 "误报"。 此项检测的初始学习期限为 7 天，在此期间，它会学习新用户的活动模式。|

>[!NOTE]
> 上述几个分析由 Microsoft Cloud App Security (MCAS) 提供支持。 若要从这些分析中获益, 需要一个已激活的 MCAS 许可证。 如果你拥有 MCAS 许可证, 则默认情况下将启用这些警报。 禁用它们:
>
> 1. 在“安全中心”边栏选项卡中，选择“安全策略”。  针对要更改的订阅，单击“编辑设置”。 
> 2. 单击“威胁检测”。 
> 3. 在“启用集成”下，取消选中“允许 Microsoft Cloud App Security 访问我的数据”，然后单击“保存”。   

>[!NOTE]
>Azure 安全中心将与安全相关的客户数据存储在与其资源相同的地理区域。 如果 Microsoft 尚未在资源的地理上部署 Azure 安全中心, 则会将数据存储在美国中。 启用 Microsoft Cloud App Security (MCAS) 后, 将根据 MCAS 的地理位置规则存储此信息。 [有关详细信息, 请参阅非区域服务的数据存储](http://azuredatacentermap.azurewebsites.net/)。
