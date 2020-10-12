---
title: Azure 安全中心的其他威胁防护
description: 了解 Azure 安全中心提供的除 Azure Defender 外的威胁防护
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 377c68cd2f0b0c132fc690cb03d7c3c5694bb52e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91438905"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure 安全中心的其他威胁防护
除内置 [Azure Defender 保护](azure-defender.md)外，Azure 安全中心还提供了以下威胁防护功能。

> [!TIP]
> 若要启用安全中心的威胁保护功能，必须在包含适用工作负载的订阅上启用 Azure Defender。
>
> 只能在资源级别为 Azure Database for MariaDB/MySQL/PostgreSQL 启用威胁防护。


## <a name="threat-protection-for-azure-network-layer"></a>针对 Azure 网络层的威胁防护 <a name="network-layer"></a>
安全中心网络层分析基于示例 [IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)（Azure 核心路由器收集的数据包头）。 根据此数据馈送，安全中心使用机器学习模型来识别和标记恶意流量活动。 安全中心还使用 Microsoft 威胁情报数据库来扩充 IP 地址。

某些网络配置可能会限制安全中心对可疑网络活动生成警报。 要使安全中心生成网络警报，请确保：
- 虚拟机有一个公共 IP 地址（或位于使用公共 IP 地址的负载均衡器上）。
- 虚拟机的网络出口流量未被外部 IDS 解决方案阻止。
- 在发生可疑通信的整小时内，为虚拟机分配了同一个 IP 地址。 这也适用于作为托管服务（例如 AKS、Databricks）的一部分创建的 VM。

有关 Azure 网络层警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azurenetlayer)。


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Azure 资源管理器（预览版）的威胁防护<a name ="management-layer"></a>
基于 Azure 资源管理器的安全中心保护层目前为预览版。

安全中心通过 Azure 资源管理器事件提供额外的保护层，该保护层被视为 Azure 的控制平面。 通过分析 Azure 资源管理器记录，安全中心可以检测 Azure 订阅环境中的非寻常或可能有害的操作。

有关 Azure 资源管理器（预览版）警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureresourceman)。


>[!NOTE]
> 上述几项分析由 Microsoft Cloud App Security 提供支持。 若要从这些分析中获益，必须激活 Cloud App Security 许可证。 如果你有 Cloud App Security 许可证，则默认会启用这些警报。 若要禁用警报：
>
> 1. 在安全中心的菜单中，选择“定价和设置”。
> 1. 选择想要更改的订阅。
> 1. 选择“威胁检测”。
> 1. 清除“允许 Microsoft Cloud App Security 访问我的数据”，然后选择“保存” 。


>[!NOTE]
>安全中心将安全相关的客户数据存储在其资源所在的地理区域。 如果 Microsoft 尚未在该资源所在的地理位置部署安全中心，则数据将存储在美国。 启用 Cloud App Security 后，将根据 Cloud App Security 的地理位置规则存储此信息。 有关详细信息，请参阅[非区域性服务的数据存储](https://azuredatacentermap.azurewebsites.net/)。

1. 设置要安装代理的工作区。 确保该工作区位于安全中心内所用的同一个订阅中，并且你对该工作区拥有读/写权限。

1. 启用“Azure Defender”，然后选择“保存” 。


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>针对 Azure Cosmos DB 的威胁防护（预览版）<a name="cosmos-db"></a>

当有人企图以非寻常和可能有害的方式访问或恶意利用 Azure Cosmos DB 帐户时，会生成 Azure Cosmos DB 警报。

有关详细信息，请参阅：

* [针对 Azure Cosmos DB 的高级威胁防护（预览版）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [针对 Azure Cosmos DB 的威胁防护警报列表（预览版）](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>针对其他 Microsoft 服务的威胁防护 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>针对 Azure WAF 的威胁防护 <a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 基于开放 Web 应用程序安全项目中的核心规则集 3.0 或 2.2.9。 WAF 会自动更新，以便在出现新漏洞后提供保护。 

如果你有 Azure WAF 许可证，则无需进行额外的配置，就会将 WAF 警报流式传输到安全中心。 有关 WAF 生成的警报的详细信息，请参阅 [Web 应用程序防火墙 CRS 规则组和规则](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>针对 Azure DDoS 防护的威胁防护 <a name="azure-ddos"></a>

众所周知，分布式拒绝服务 (DDoS) 攻击是很容易执行的。 它们已成为极大的安全隐患，尤其是要将应用程序转移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击能够以任何可通过 Internet 访问的终结点作为目标。

若要抵御 DDoS 攻击，请购买 Azure DDoS 防护许可证，并确保遵循有关应用程序设计的最佳做法。 DDoS 防护提供不同的服务层级。 有关详细信息，请参阅 [Azure DDoS 防护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有关 Azure DDoS 防护警报的列表，请参阅[警报参考表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>后续步骤
若要详细了解这些威胁防护功能生成的安全警报，请参阅以下文章：

* [所有 Azure 安全中心警报的参考表](alerts-reference.md)
* [Azure 安全中心中的安全警报](security-center-alerts-overview.md)
* [在 Azure 安全中心内管理和响应安全警报](security-center-managing-and-responding-alerts.md)
* [导出安全警报和建议（预览版）](continuous-export.md)