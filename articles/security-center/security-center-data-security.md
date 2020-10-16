---
title: Azure 安全中心数据安全性 | Microsoft Docs
description: 本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 8e92c893d97ce9fadfa2d40476631c2b9ba5d177
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999264"
---
# <a name="azure-security-center-data-security"></a>Azure 安全中心数据安全性

为了帮助客户防止、检测和应对威胁，Azure 安全中心将收集和处理安全相关的数据，包括配置信息、元数据、事件日志等等。 从编程到服务运营，Microsoft 都严格遵守相关法规与安全准则。

本文介绍如何在安全中心管理数据和确保数据安全性。

## <a name="data-sources"></a>数据源
安全中心将分析以下源中的数据，提供安全状态视图、识别漏洞、建议缓解措施，并检测现行的威胁：

- **Azure 服务**：通过与 Azure 服务的资源提供程序通信，使用已部署的 Azure 服务的配置信息。
- **网络流量**：使用从 Microsoft 基础结构中采样的网络流量元数据，例如源/目标 IP/端口、数据包大小以及网络协议。
- **合作伙伴解决方案**：使用来自集成合作伙伴解决方案（例如防火墙和反恶意软件解决方案）的安全警报。
- **计算机**：使用配置详细信息和有关安全事件的信息，如 Windows 事件和审核日志，以及计算机中的系统日志消息。


## <a name="data-protection"></a>数据保护

### <a name="data-segregation"></a>数据隔离
服务中每个组件的数据都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。

### <a name="data-access"></a>数据访问
为了提供安全建议及调查潜在安全威胁，Microsoft 人员可能会访问 Azure 服务收集或分析的信息，包括进程创建事件以及其他项目，其中可能会意外地包括计算机中的客户数据或个人数据。 

我们遵守 [Microsoft Online Services 数据保护附录](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)，其中指出，Microsoft 不会将客户数据或其衍生信息用于任何广告目的或类似的商业目的。 我们只会根据需要将客户数据用于向用户提供 Azure 服务，包括用于与提供这些服务相对应的目的。 用户保留对客户数据的所有权限。

### <a name="data-use"></a>数据使用
Microsoft 使用多个租户所使用的模式和威胁情报增强预防和检测威胁的能力；执行过程中遵循[隐私声明](https://privacy.microsoft.com/privacystatement)中所述的隐私承诺。

## <a name="manage-data-collection-from-machines"></a>管理计算机中的数据收集
在 Azure 中启用安全中心后，即为每个 Azure 订阅启用了数据收集功能。 也可以在安全中心中为订阅启用数据收集。 启用数据收集后，安全中心即可在所有受支持的现有 Azure 虚拟机以及任何新创建的虚拟机中预配 Log Analytics 代理。

Log Analytics 代理扫描各种安全相关配置和事件，并将其收集到 [Windows 事件跟踪](https://docs.microsoft.com/windows/win32/etw/event-tracing-portal) (ETW) 的跟踪中。 另外，在运行计算机的过程中，操作系统会引发事件日志事件。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。 Log Analytics 代理读取事件日志条目和 ETW 跟踪，并将其复制到工作区进行分析。 Log Analytics 代理还启用进程创建事件和命令行审核。

如果不使用 Azure Defender，也可以在“安全策略”中从虚拟机禁用数据收集。 受 Azure Defender 保护的订阅需要数据收集。 即使禁用数据收集，也仍会启用 VM 磁盘快照和项目收集。

可以指定存储从计算机收集的数据的工作区和区域。 默认将从计算机中收集的数据存储在最近的工作区中，如下表所示：

| VM 地区                              | 工作区地区 |
|-------------------------------------|---------------|
| 美国、巴西、南非 | United States |
| Canada                              | Canada        |
| 欧洲（不包括英国）   | 欧洲        |
| United Kingdom                      | United Kingdom |
| 亚洲（不包括印度、日本、韩国、中国）   | 亚太区  |
| 韩国                              | 亚太区  |
| 印度                               | 印度         |
| 日本                               | 日本         |
| 中国                               | 中国         |
| 澳大利亚                           | 澳大利亚     |
|||

> [!NOTE]
> 适用于存储的 Azure Defender 根据相关 Azure 资源的位置按区域存储项目。 有关详细信息，请参阅[适用于存储的 Azure Defender 简介](defender-for-storage-introduction.md)。


## <a name="data-consumption"></a>数据使用

客户可以从以下数据流访问安全中心相关数据：


|Stream  |数据类型  |
|---------|---------|
|[Azure 活动日志](../azure-monitor/platform/activity-log.md)| 所有安全警报、经审核的安全中心[实时](security-center-just-in-time.md)访问请求，以及由[自适应应用程序控制](security-center-adaptive-application.md)生成的所有警报 |
|[Azure Monitor 日志](../azure-monitor/platform/data-platform.md)|所有安全警报。|
|[Azure Resource Graph](../governance/resource-graph/overview.md)|安全警报、安全建议、漏洞评估结果、安全分数信息、合规性检查的状态等。|
|[Azure 安全中心 REST API](https://docs.microsoft.com/rest/api/securitycenter/)|安全警报、安全建议等。 .|
|||

## <a name="next-steps"></a>后续步骤

本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。 

若要详细了解 Azure 安全中心，请参阅：

- [什么是 Azure 安全中心？](security-center-introduction.md)
