---
title: Azure 安全中心的虚拟机建议 | Microsoft Docs
description: 本文档介绍了 Azure 安全中心有关如何帮助保护虚拟机和计算机以及 Web 应用和应用服务环境的建议。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544972"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>了解 Azure 安全中心资源建议


## <a name="recommendations"></a>建议
使用下表作为参考，以帮助你了解可用的计算和应用服务建议以及每个建议在应用时的作用。

### <a name="computers"></a>计算机
| 建议 | 描述 |
| --- | --- |
| [为订阅启用数据收集](security-center-enable-data-collection.md) |建议为每个订阅和订阅中的所有虚拟机 (VM) 开启安全策略中的数据收集。 |
| [为 Azure 存储帐户启用加密](security-center-enable-encryption-for-storage-account.md) | 建议为静态数据启用 Azure 存储服务加密。 存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。 SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。 若要了解详细信息，请参阅[静态数据的存储服务加密](../storage/common/storage-service-encryption.md)。</br>只有 Resource Manager 存储帐户支持 SSE。 目前不支持经典存储帐户。 若要了解经典部署模型和 Resource Manager 部署模型，请参阅 [Azure 部署模型](../azure-classic-rm.md)。 |
| [修正安全配置](security-center-remediate-os-vulnerabilities.md) |建议使用推荐的安全配置规则调整 OS 配置，例如不允许保存密码。 |
| [应用系统更新](security-center-apply-system-updates.md) |建议在 VM 上部署缺少的系统安全和重要更新。 |
| [应用实时网络访问控制](security-center-just-in-time.md) | 建议应用实时 VM 访问。 实时功能处于预览状态，并在安全中心的标准层上可用。 请参阅[定价](security-center-pricing.md)，详细了解安全中心的定价层。 |
| [在系统更新后重启](security-center-apply-system-updates.md#reboot-after-system-updates) |建议重启 VM 以完成应用系统更新的过程。 |
| [安装终结点保护](security-center-install-endpoint-protection.md) |建议在 VM（仅 Windows VM）上预配反恶意程序。 |
| [启用 VM 代理](security-center-enable-vm-agent.md) |使你能够查看需要 VM 代理的 VM。 若要设置修补程序扫描、基线扫描以及反恶意程序，则必须在 VM 上安装 VM 代理。 对于从 Azure 市场部署的 VM，默认安装 VM 代理。 文章 [VM 代理和扩展 - 第 2 部分](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供有关如何安装 VM 代理的信息。 |
| [应用磁盘加密](security-center-apply-disk-encryption.md) |建议使用 Azure 磁盘加密（Windows 和 Linux VM）对 VM 磁盘进行加密。 对于 VM 上的 OS 和数据卷，建议使用加密。 |
| [更新 OS 版本](security-center-update-os-version.md) |建议将云服务的操作系统 (OS) 版本更新为 OS 系列可用的最新版本。  若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。 |
| [未安装漏洞评估](security-center-vulnerability-assessment-recommendations.md) |建议在 VM 上安装漏洞评估解决方案。 |
| [修正漏洞](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |使你可以查看由安装在 VM 上的漏洞评估解决方案检测到的系统和应用程序漏洞。 |

### 应用服务 <a name="app-services"></a>
| 建议 | 描述 |
| --- | --- |
| 应该只能通过 HTTPS 访问应用服务 | 建议你限制为仅通过 HTTPS 访问应用服务。 |
| 应为 Web 应用程序禁用 Web 套接字| 建议你仔细检查 Web 应用程序中 Web 套接字的使用。  Web 套接字协议容易受到不同类型的安全威胁的攻击。 |
| 对 Web 应用程序使用自定义域 | 建议使用自定义域保护 Web 应用程序免受常见攻击（钓鱼和其他 DNS 相关攻击）的威胁。 |
| 为 Web 应用程序配置 IP 限制 | 建议你定义允许访问应用程序的 IP 地址列表。  使用 IP 限制保护 Web 应用程序免受常见攻击的威胁。 |
| 不允许所有 ('*') 资源访问你的应用程序 | 建议不要将 WEBSITE_LOAD_CERTIFICATES 参数设置为“ *”。将参数设置为“* ”表示所有证书都将加载到 Web 应用程序个人证书存储。  这可能导致滥用最小特权原则，因为站点在运行时不太可能需要访问所有证书。 |
| CORS 不应允许所有资源都能访问你的应用程序 | 建议你仅允许必需的域与 Web 应用程序进行交互。 跨源资源共享 (CORS) 不应允许所有域都能访问你的 Web 应用程序。 |
| 对 Web 应用程序使用受支持的最新版 .NET Framework | 建议使用最新的 .NET Framework 版本以使用最新安全类。 使用较旧的类和类型可能会使应用程序易受攻击。 |
| 对 Web 应用程序使用受支持的最新版 Java | 建议使用最新的 Java 版本以使用最新安全类。 使用较旧的类和类型可能会使应用程序易受攻击。 |
| 对 Web 应用程序使用受支持的最新版 PHP | 建议使用最新的 PHP 版本以使用最新安全类。 使用较旧的类和类型可能会使应用程序易受攻击。 |
| [添加 Web 应用程序防火墙](security-center-add-web-application-firewall.md) |建议部署 web 终结点的 Web 应用程序防火墙 (WAF)。 为任何面向公众的 IP（实例级 IP 或负载均衡 IP）显示 WAF 建议，该 IP 具有与开放入站 Web 端口 (80,443) 关联的网络安全组。</br></br>安全中心建议设置 WAF，有助于防范针对虚拟机和应用服务环境上 Web 应用程序的攻击。 应用服务环境 (ASE) 是 Azure 应用服务的[高级](https://azure.microsoft.com/pricing/details/app-service/)服务计划选项，可提供完全隔离和专用的环境，以便安全地运行 Azure 应用服务应用。 若要了解有关 ASE 的详细信息，请参阅[应用服务环境文档](../app-service/environment/intro.md)。</br></br>可以通过将应用程序添加到现有的 WAF 部署来保护安全中心中的多个 Web 应用程序。 |
| [完成应用程序保护](security-center-add-web-application-firewall.md#finalize-application-protection) |若要完成 WAF 配置，则流量必须重新路由到 WAF 设备。 遵循此建议，完成必要的安装程序更改。 |
| 对 Web 应用程序使用受支持的最新版 Node.js | 建议使用最新的 Node.js 版本以使用最新安全类。 使用较旧的类和类型可能会使应用程序易受攻击。 |
| CORS 不应允许所有资源都能访问函数应用 | 建议你仅允许必需的域与 Web 应用程序进行交互。 跨源资源共享 (CORS) 不应允许所有域都能访问你的函数应用程序。 |
| 对函数应用使用自定义域 | 建议使用自定义域保护函数应用免受常见攻击（钓鱼和其他 DNS 相关攻击）的威胁。 |
| 对函数应用配置 IP 限制 | 建议你定义允许访问应用程序的 IP 地址列表。 使用 IP 限制保护函数应用免受常见攻击的威胁。 |
| 应该只能通过 HTTPS 访问函数应用 | 建议你限制为仅通过 HTTPS 访问函数应用。 |
| 应对函数应用禁用远程调试 | 如果不再需要使用函数应用调试，建议禁用该功能。 远程调试需要在函数应用上打开入站端口。 |
| 应对函数应用禁用 Web 套接字 | 建议你仔细检查函数应用中 Web 套接字的使用。 Web 套接字协议容易受到不同类型的安全威胁的攻击。 |


## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [在 Azure 安全中心监视标识和访问](security-center-identity-access.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
