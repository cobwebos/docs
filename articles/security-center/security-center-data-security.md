---
title: "Azure 安全中心数据安全性 | Microsoft Docs"
description: "本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f20d77a43185a6b22a5da1ee1c2744707df13dae


---
# <a name="azure-security-center-data-security"></a>Azure 安全中心数据安全性
为了帮助客户防止、检测和应对威胁，Azure 安全中心会收集和处理有关 Azure 资源的数据，包括配置信息、元数据、事件日志、故障转储文件等。 我们强力承诺保护此类数据的隐私和安全性。 从服务的编码到运营，Microsoft 都严格遵守相关法规与安全准则。 

本文介绍如何在 Azure 安全中心管理数据和确保数据安全性。

## <a name="data-sources"></a>数据源
Azure 安全中心分析以下来源的数据：

* Azure 服务：通过与 Azure 服务的资源提供程序通信，读取已部署的该服务的配置信息。
* 网络流量：从 Microsoft 的基础结构读取示例性网络流量元数据，例如源/目标 IP/端口、数据包大小以及网络协议。
* 合作伙伴解决方案：从集成合作伙伴解决方案（例如防火墙和防恶意软件解决方案）收集安全警报。 该数据存储在目前位于美国的 Azure 安全中心存储器中。
* 用户的虚拟机：Azure 安全中心可以通过数据收集代理从用户的虚拟机收集配置信息以及有关安全事件（例如 Windows 事件和审核日志、IIS 日志、系统日志消息、故障转储文件）的信息。 更多详细信息，请参阅下面的“管理数据收集”部分。  

另外，有关安全警报、建议以及安全运行状况的信息存储在目前位于美国的 Azure 安全中心存储器中。 该信息可能包括根据需要从用户虚拟机收集的相关配置信息和安全事件，目的是为用户提供安全警报、建议或安全运行状况。

## <a name="data-protection"></a>数据保护
**数据隔离**：服务的每个组件的数据都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。 另外，从虚拟机收集的数据存储在存储帐户中。

**数据访问**：为了提供安全建议和调查潜在的安全威胁，Microsoft 人员可能会访问通过 Azure 服务收集或分析的信息，包括故障转储文件。 故障转储文件和进程创建事件可能会无意中包括虚拟机的客户数据或个人数据。 我们遵守 [Microsoft Online Services 条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和[隐私声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)，这些条款和声明指出，Microsoft 不会将客户数据或其衍生信息用于任何广告目的或类似的商业目的。 我们只会根据需要将客户数据用于向用户提供 Azure 服务，包括用于与提供这些服务相对应的目的。 用户保留对客户数据的所有权限。

**数据使用**：Microsoft 使用多个租户所使用的模式和威胁情报增强用户预防和检测威胁的能力；执行过程中遵循[隐私声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)中所述的隐私承诺。

**数据位置**：为运行虚拟机的每个区域指定一个存储帐户。 这样即可将数据存储在一个区域，而从虚拟机收集数据时，虚拟机也位于该区域。 此数据（包括故障转储文件）将持久存储在存储帐户中。 该服务还将有关安全警报（包括来自集成合作伙伴解决方案的警报）、建议以及安全运行状况的信息存储在目前位于美国的 Azure 安全中心存储器中。

## <a name="managing-data-collection-from-virtual-machines"></a>管理从虚拟机进行的数据收集
选择启用 Azure 安全中心以后，即为每个订阅打开了数据收集功能。 可以在 Azure 安全中心仪表板的“安全策略”部分关闭数据收集功能。 启用数据收集功能以后，Azure 安全中心即可在所有现有的受支持的虚拟机以及任何新创建的虚拟机中预配 Azure 监视代理。 Azure 安全监视扩展会扫描各种安全相关配置和事件，并将其收集到 [Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) 的跟踪中。 另外，在运行计算机的过程中，操作系统会引发事件日志事件。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址、已登录用户、租户 ID。 Azure 监视代理读取事件日志条目和 ETW 跟踪，并将其复制到存储帐户进行分析。 

将为每个有用户的虚拟机运行的区域指定一个存储帐户，从该区域的虚拟机收集的数据存储在该帐户中。 这样即可轻松地将数据保存在同一地理区域，确保维护隐私和数据所有权。 可以在 Azure 安全中心仪表板的“安全策略”部分为每个区域配置存储帐户。

Azure 监视代理还将故障转储文件复制到存储帐户。  Azure 安全中心收集故障转储文件的临时副本并对其进行分析，目的是找出攻击者尝试利用漏洞并成功进行了破坏的证据。  Azure 安全中心执行此分析时所在的地理区域与存储帐户所在的地理区域相同，在分析完成后会删除临时副本。

可以随时禁止从虚拟机进行数据收集，删除 Azure 安全中心在以前安装的任何监视代理。

## <a name="see-also"></a>另请参阅
本文档介绍如何在 Azure 安全中心管理数据和确保数据安全性。 若要详细了解 Azure 安全中心，请参阅：

* [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md) （Azure 安全中心规划和操作指南）- 了解如何规划并理解设计注意事项，以便采用 Azure 安全中心。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章




<!--HONumber=Dec16_HO2-->


