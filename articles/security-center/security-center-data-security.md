---
title: "Azure 安全中心数据安全性 | Microsoft Docs"
description: "本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0


---
# <a name="azure-security-center-data-security"></a>Azure 安全中心数据安全性
为了帮助客户防止、检测和应对威胁，Azure 安全中心将收集和处理安全相关的数据，包括配置信息、元数据、事件日志、故障转储文件，等等。 从编程到服务运营，Microsoft 都严格遵守相关法规与安全准则。

本文介绍如何在 Azure 安全中心管理数据和确保数据安全性。


## <a name="data-sources"></a>数据源
Azure 安全中心将分析以下源中的数据，提供安全状态视图、识别漏洞、建议缓解措施，并检测现行的威胁：

- Azure 服务：通过与 Azure 服务的资源提供程序通信，使用已部署的 Azure 服务的配置信息。
- 网络流量：使用从 Microsoft 基础结构中采样的网络流量元数据，例如源/目标 IP/端口、数据包大小以及网络协议。
- 合作伙伴解决方案：使用来自集成合作伙伴解决方案（例如防火墙和防恶意软件解决方案）的安全警报。
- 用户的虚拟机：使用用户虚拟机中的配置信息，以及有关安全事件的信息，例如 Windows 事件和审核日志、IIS 日志、系统日志消息和故障转储文件。


## <a name="data-protection"></a>数据保护
**数据隔离**：服务的每个组件的数据都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。

**数据访问**：为了提供安全建议和调查潜在的安全威胁，Microsoft 人员可能访问 Azure 服务收集或分析的信息，包括故障转储文件、进程创建事件、VM 磁盘快照和项目，因此可能意外地包括了虚拟机中的客户数据或个人数据。 我们遵守 [Microsoft Online Services 条款和隐私声明](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，其中指出，Microsoft 不会出于广告或类似的商业目的利用客户数据，或者从客户数据衍生信息。 我们只会根据需要将客户数据用于向用户提供 Azure 服务，包括用于与提供这些服务相对应的目的。 用户保留对客户数据的所有权限。

**数据使用**：Microsoft 使用多个租户所使用的模式和威胁情报增强用户预防和检测威胁的能力；执行过程中遵循[隐私声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)中所述的隐私承诺。

## <a name="data-location"></a>数据位置
**用户的存储帐户**：可为运行虚拟机的每个区域指定一个存储帐户。 这样即可将数据存储在一个区域，而从虚拟机收集数据时，虚拟机也位于该区域。 此数据（包括故障转储文件）将持久存储在存储帐户中。 VM 磁盘快照存储在 VM 磁盘所在的同一存储帐户中。

**Azure 安全中心存储**：有关安全警报（包括合作伙伴警报）、建议和安全运行状况的信息将集中存储，存储位置目前位于美国。 该信息可能包括根据需要从用户虚拟机收集的相关配置信息和安全事件，目的是为用户提供安全警报、建议或安全运行状况。

Azure 安全中心收集故障转储文件的临时副本并对其进行分析，目的是找出攻击者尝试利用漏洞并成功进行了破坏的证据。 Azure 安全中心在工作区所在的同一地理位置执行此分析，分析完成后，将删除临时副本。

计算机项目集中存储在 VM 所在的同一区域。


## <a name="managing-data-collection-from-virtual-machines"></a>管理从虚拟机进行的数据收集
选择启用 Azure 安全中心以后，即为每个订阅打开了数据收集功能。 可在 Azure 安全中心的“安全策略”部分中打开数据收集。 启用数据收集功能以后，Azure 安全中心即可在所有现有的受支持的虚拟机以及任何新创建的虚拟机中预配 Azure 监视代理。 Azure 安全监视扩展将扫描各种安全相关配置和事件，并将其收集到 [Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的跟踪中。 另外，在运行计算机的过程中，操作系统会引发事件日志事件。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。 Azure 监视代理读取事件日志条目和 ETW 跟踪，并将其复制到存储帐户进行分析。

可以随时禁止从虚拟机进行数据收集，删除 Azure 安全中心在以前安装的任何监视代理。 即使禁用数据收集，也仍会启用 VM 磁盘快照和项目收集。


## <a name="see-also"></a>另请参阅
本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。 若要详细了解 Azure 安全中心，请参阅：

* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md) （Azure 安全中心规划和操作指南）- 了解如何规划并理解设计注意事项，以便采用 Azure 安全中心。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章



<!--HONumber=Feb17_HO1-->


