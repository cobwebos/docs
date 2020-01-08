---
title: Azure 服务层威胁检测-Azure 安全中心
description: 本主题介绍 Azure 安全中心提供的 Azure 服务层警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665697"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Azure 安全中心内 Azure 服务层的威胁检测

本主题提供监视以下 Azure 服务层时可用的 Azure 安全中心警报：

* [Azure 网络层](#network-layer)
* [Azure 管理层（Azure 资源管理器）（预览版）](#management-layer)
* [Azure Key Vault](#azure-keyvault)

## Azure 网络层<a name="network-layer"></a>

安全中心网络层分析基于示例[IPFIX 数据](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，这些数据是由 Azure 核心路由器收集的数据包标头。 根据此数据馈送，安全中心将使用机器学习模型来识别和标记恶意流量活动。 安全中心还使用 Microsoft 威胁智能数据库来丰富 IP 地址。

某些网络配置可能会限制安全中心在可疑网络活动上生成警报。 要使安全中心生成网络警报，请确保：

- 虚拟机有一个公共 IP 地址（或在具有公共 IP 地址的负载均衡器上）。

- 外部 IDS 解决方案不阻止虚拟机的网络出口流量。

- 已为虚拟机分配的 IP 地址与发生可疑通信的整个小时相同。 这也适用于作为托管服务的一部分创建的 Vm （例如，AKS、Databricks）。

有关 Azure 网络层警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azurenetlayer)。

有关安全中心如何使用网络相关信号来应用威胁防护的详细信息，请参阅[安全中心中的启发式 DNS 检测](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。


## Azure 管理层（Azure 资源管理器）（预览版）<a name ="management-layer"></a>

基于 Azure 资源管理器的安全中心保护层目前处于预览阶段。

安全中心通过使用 Azure 资源管理器事件（被视为 Azure 的控制平面）提供额外的保护层。 通过分析 Azure 资源管理器记录，安全中心检测到 Azure 订阅环境中的异常或潜在有害操作。

有关 Azure 资源管理器（预览版）警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 上述几个分析由 Microsoft Cloud App Security 提供支持。 若要从这些分析中获益，必须激活 Cloud App Security 许可证。 如果有 Cloud App Security 许可证，则默认情况下将启用这些警报。 禁用它们：
>
> 1. 在 "**安全中心**" 边栏选项卡中，选择 "**安全策略**"。 对于要更改的订阅，请选择 "**编辑设置**"。
> 2. 选择 "**威胁检测**"。
> 3. 在 "**启用集成**" 下，清除 "**允许 Microsoft Cloud App Security 访问我的数据**"，然后选择 "**保存**"。

>[!NOTE]
>安全中心将与安全相关的客户数据存储在与其资源相同的地理区域。 如果 Microsoft 尚未在资源的地理上部署安全中心，则会将数据存储在美国中。 启用 Cloud App Security 时，此信息按 Cloud App Security 的地理位置规则存储。 有关详细信息，请参阅[非区域服务的数据存储](https://azuredatacentermap.azurewebsites.net/)。

## Azure Key Vault （预览）<a name="azure-keyvault"></a>

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

Azure 安全中心包含适用于 Azure Key Vault 的 Azure 本机高级威胁防护，提供额外的安全智能层。 安全中心检测到 Key Vault 帐户访问或利用异常的潜在有害尝试。 这一层保护使你可以在不是安全专家的情况下解决威胁，而无需管理第三方安全监视系统。  

当发生异常活动时，安全中心会显示警报，还可以选择通过电子邮件将其发送给订阅管理员。 这些警报包括可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

> [!NOTE]
> 此服务目前在 Azure 政府版和主权云区域中不可用。

有关 Azure Key Vault 警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azurekv)。
