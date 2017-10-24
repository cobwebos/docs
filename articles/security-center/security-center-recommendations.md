---
title: "管理 Azure 安全中心的安全建议 | Microsoft Docs"
description: "本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: terrylan
ms.openlocfilehash: 32735a7dfaafd8bbfb983dd809b2204fb1b2fa10
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>管理 Azure 安全中心的安全建议
本文档介绍如何使用 Azure 安全中心中的建议帮助你保护 Azure 资源。

> [!NOTE]
> 本文档通过使用示例部署介绍该服务。  本文档不是一份分步指南。
>
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？
安全中心定期分析 Azure 资源的安全状态。 安全中心识别到潜在的安全漏洞时，会创建建议。 此建议指导完成配置所需控件的过程。

## <a name="implementing-security-recommendations"></a>实施安全性建议
### <a name="set-recommendations"></a>设置建议
在[设置 Azure 安全中心的安全策略](security-center-policies.md)中，了解到：

* 配置安全策略。
* 启用数据收集。
* 选择视作安全策略的一部分的建议。

当前的策略建议以系统更新、基线规则、反恶意程序、子网和网络接口的[网络安全组](../virtual-network/virtual-networks-nsg.md)、SQL 数据库审核、SQL 数据库透明数据加密和 Web 应用程序防火墙为中心。  [设置安全策略](security-center-policies.md)提供每个建议选项的说明。

### <a name="monitor-recommendations"></a>监视建议
设置安全策略之后，安全中心将分析资源的安全状态，以识别潜在的漏洞。 通过“概述”下的“建议”磁贴，可以知道安全中心定义的建议总数。

![“建议”磁贴][1]

若要查看每项建议的详细信息，请选择“概述”下的“建议”磁贴。 这将打开“建议”。

![筛选器建议][2]

建议以表格形式显示，其中每一行都表示一个特定的建议。 此表的列有：

* **说明**：解释建议以及处理此建议所需执行的操作。
* **资源**：列出了此建议适用的资源。
* **状态**：描述该建议的当前状态：
  * **未解决**：建议尚未得到处理。
  * **正在进行**：目前已将建议应用到相关资源，不需用户采取行动。
  * **已解决**：已完成处理此建议（在这种情况下，该行将灰显）。
* **严重性**：描述该特定建议的严重性：
  * **高**：重要资源（如应用程序、VM、网络安全组）存在漏洞，需要提请注意。
  * **中**：存在漏洞，需要采取非关键步骤或额外步骤来消除它或完成某个过程。
  * **低**：存在需要解决的漏洞，但不需立即处理。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）

使用下表作为参考来帮助了解可用的建议，以及应用建议后，每个建议的做法。

> [!NOTE]
> 想要了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)之间的差异。
>
>

| 建议 | 说明 |
| --- | --- |
| [为订阅启用数据收集](security-center-enable-data-collection.md) |建议为每个订阅、所有 Azure 虚拟机 (VM) 和非 Azure 计算机开启安全策略中的数据收集。 |
| [修正 OS 漏洞](security-center-remediate-os-vulnerabilities.md) |建议 OS 配置匹配推荐的配置规则，例如不允许保存密码。 |
| [应用系统更新](security-center-apply-system-updates.md) |建议在 Windows 和 Linux VM 及计算机上部署缺少的系统安全和重要更新。 |
| [应用实时网络访问控制](security-center-just-in-time.md) | 建议应用实时 VM 访问。 实时功能处于预览状态，并在安全中心的标准层上可用。 请参阅[定价](security-center-pricing.md)，详细了解安全中心的定价层。 |
| [在系统更新后重启](security-center-apply-system-updates.md#reboot-after-system-updates) |建议重启 VM 以完成应用系统更新的过程。 |
| [添加 Web 应用程序防火墙](security-center-add-web-application-firewall.md) |建议部署 web 终结点的 Web 应用程序防火墙 (WAF)。 为任何面向公众的 IP（实例级 IP 或负载均衡 IP）显示 WAF 建议，该 IP 具有与开放入站 Web 端口 (80,443) 关联的网络安全组。 </br>安全中心建议设置 WAF，有助于防范针对虚拟机和应用服务环境上 Web 应用程序的攻击。 应用服务环境 (ASE) 是 Azure 应用服务的[高级](https://azure.microsoft.com/pricing/details/app-service/)服务计划选项，可提供完全隔离和专用的环境，以便安全地运行 Azure 应用服务应用。 若要了解有关 ASE 的详细信息，请参阅[应用服务环境文档](../app-service/environment/intro.md)。</br>可以通过将应用程序添加到现有的 WAF 部署来保护安全中心中的多个 Web 应用程序。 |
| [完成应用程序保护](security-center-add-web-application-firewall.md#finalize-application-protection) |若要完成 WAF 配置，则流量必须重新路由到 WAF 设备。 遵循此建议，完成必要的安装程序更改。 |
| [添加下一代防火墙](security-center-add-next-generation-firewall.md) |建议从 Microsoft 合作伙伴添加下一代防火墙 (NGFW)，以增强安全保护。 |
| [仅通过 NGFW 路由流量](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |建议配置通过 NGFW 强制将流量入站到 VM 的网络安全组 (NSG) 规则。 |
| [安装终结点保护](security-center-install-endpoint-protection.md) |建议在 VM（仅 Windows VM）上预配反恶意程序。 |
| [在子网或虚拟机上启用网络安全组](security-center-enable-network-security-groups.md) |建议在子网或 VM 上启用 NSG。 |
| [通过面向 Internet 的终结点限制访问](security-center-restrict-access-through-internet-facing-endpoints.md) |建议为 NSG 配置入站流量规则。 |
| [在 SQL 服务器上启用审核与威胁检测](security-center-enable-auditing-on-sql-servers.md) |建议开启 Azure SQL Server 的审核与威胁检测。 （仅 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。） |
| [在 SQL 数据库上启用审核与威胁检测](security-center-enable-auditing-on-sql-databases.md) |建议开启 Azure SQL 数据库的审核与威胁检测。 （仅 Azure SQL 服务。 不包括在虚拟机上运行的 SQL。） |
| [启用 SQL 数据库的透明数据加密](security-center-enable-transparent-data-encryption.md) |建议启用 SQL 数据库加密。 （仅 Azure SQL 服务。） |
| [启用 VM 代理](security-center-enable-vm-agent.md) |使你能够查看需要 VM 代理的 VM。 若要设置修补程序扫描、基线扫描以及反恶意软件程序，则必须在 VM 上安装 VM 代理。 对于从 Azure Marketplace 部署的 VM，默认安装 VM 代理。 文章 [VM 代理和扩展 - 第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供有关如何安装 VM 代理的信息。 |
| [应用磁盘加密](security-center-apply-disk-encryption.md) |建议使用 Azure 磁盘加密（Windows 和 Linux VM）对 VM 磁盘进行加密。 对于 VM 上的 OS 和数据卷，建议使用加密。 |
| [提供安全联系人详细信息](security-center-provide-security-contact-details.md) |建议为每个订阅提供安全联系人详细信息。 联系人详细信息为电子邮件地址和电话号码。 如果安全团队发现资源遭到盗用，则会采用此信息与你联系。 |
| [更新 OS 版本](security-center-update-os-version.md) |建议将云服务的操作系统 (OS) 版本更新为 OS 系列可用的最新版本。  若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。 |
| [未安装漏洞评估](security-center-vulnerability-assessment-recommendations.md) |建议在 VM 上安装漏洞评估解决方案。 |
| [修正漏洞](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |使你可以查看由安装在 VM 上的漏洞评估解决方案检测到的系统和应用程序漏洞。 |
| [为 Azure 存储帐户启用加密](security-center-enable-encryption-for-storage-account.md) | 建议为静态数据启用 Azure 存储服务加密。 存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。 SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。 若要了解详细信息，请参阅[静态数据的存储服务加密](../storage/common/storage-service-encryption.md)。</br>只有 Resource Manager 存储帐户支持 SSE。 |

可筛选和取消建议。

1. 选择“建议”边栏选项卡上的“筛选器”。 此时会打开“筛选器”边栏选项卡，选择要查看严重性和状态值。

2. 如果确定某个建议不适用，即可取消此建议，将其从视图中筛除。 有两种方法可取消建议。 一种方法是右键单击项目，并选择“取消”。 另一种方法是将鼠标悬停在某个项目上，单击右侧显示的三个点，选择“取消”。 可以单击“筛选器”，选择“已取消”，查看已取消的建议。

    ![取消建议][3]

### <a name="apply-recommendations"></a>应用建议
查看所有建议后，决定应首先应用哪个建议。 我们建议使用严重性等级作为主要参数来评估应首先应用的建议。

在上面的建议表中，选择某建议，将其作为如何应用建议的示例进行查看。

## <a name="next-steps"></a>后续步骤
在本文档中，已向你介绍安全中心的安全建议。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md) （Azure 安全中心的安全运行状况监视）- 了解如何监视 Azure 资源的运行状况。
* [在 Azure 安全中心管理和响应安全警报](security-center-managing-and-responding-alerts.md) — 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure Security Center FAQ](security-center-faq.md) （Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
