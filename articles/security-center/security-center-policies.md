---
title: "在 Azure 安全中心设置安全策略 | Microsoft Docs"
description: "本文档介绍了如何在 Azure 安全中心配置安全策略。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: aefec15c72c6cf8389a29b03be70abb4c7f020b9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="set-security-policies-in-azure-security-center"></a>在 Azure 安全中心设置安全策略
本文档介绍如何在安全中心配置安全策略，指导用户完成执行此任务所必需的步骤。

>[!NOTE] 
>自 2017 年 6 月初开始，安全中心将使用 Microsoft Monitoring Agent 来收集和存储数据。 请参阅 [Azure 安全中心平台迁移](security-center-platform-migration.md)，了解详细信息。 本文中的信息表示转换到 Microsoft Monitoring Agent 后的安全中心功能。
>

## <a name="what-are-security-policies"></a>什么是安全策略？
安全策略定义一组控件，这些控件是针对指定订阅中的资源建议的。 在安全中心，用户需根据公司安全需求和应用程序类型或每个订阅中数据的敏感性，为 Azure 订阅定义策略。

例如，开发或测试用资源的安全要求可能不同于生产应用型资源。 同样，使用管控数据（例如个人身份信息）的应用程序可能需要更高级别的安全性。 在 Azure 安全中心启用的安全策略可以通过安全建议和监视帮助用户确定可能的漏洞并缓解威胁造成的危害。 阅读 [Azure Security Center Planning and Operations Guide](security-center-planning-and-operations-guide.md) （Azure 安全中心规划和操作指南），详细了解如何确定适当的选项。

## <a name="set-security-policies"></a>设置安全策略
可为每个订阅配置安全策略。 若要修改安全策略，用户必须是该订阅的所有者或参与者。 登录到 Azure 门户，按照后续步骤在安全中心配置安全策略：

1. 单击“安全中心”仪表板中的“策略”磁贴。
2. 在打开的“安全策略”边栏选项卡中，选择要启用安全策略的订阅。

    ![定义策略](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. 所选订阅的“安全策略”边栏选项卡在打开时会显示一组选项。 此边栏选项卡中的可用选项包括：

   * **预防策略**：使用此选项配置每个订阅的策略。  
   * **电子邮件通知**：使用此选项配置电子邮件通知，让其在每天第一次出现警报时发送，以及在警报的严重性高的情况下发送。 只能根据订阅策略配置电子邮件首选项。 请阅读 [Provide security contact details in Azure Security Center](security-center-provide-security-contact-details.md) （在 Azure 安全中心提供安全方面的联系细节），详细了解如何配置电子邮件通知。
   * **定价层**：使用此选项升级所选定价层。 请参阅[安全中心定价](security-center-pricing.md)，详细了解各种定价选项。
4. 确保“从虚拟机收集数据”选项为“启用”。 通过此选项，可使用 Microsoft Monitoring Agent（Operations Management Suite 和 Log Analytics 服务同样使用此代理）实现对现有和新资源的自动日志收集。 通过此代理收集的数据存储在与 Azure 订阅关联的现有 Log Analytics 工作区或新工作区中，具体取决于 VM 的地理位置。

5. 在“安全策略”边栏选项卡中，单击“保护策略”查看可用选项。 单击“启用”，启用此订阅相关的安全建议。

    ![选择安全策略](./media/security-center-policies/security-center-policies-fig4-newUI.png)

若要了解每个选项，可使用下表作为参考：

| 策略 | 当状态为“启用”时 |
| --- | --- |
| 系统更新 |从 Windows 更新或 Windows Server Update Services 检索包含可用安全更新和关键更新的每日列表。 检索的列表取决于为该虚拟机配置的服务，并且会建议用户应用缺少的更新。 对于 Linux 系统，该策略会使用发行版提供的程序包管理系统确定包含可用更新的程序包。 此外还会查看是否存在 [Azure 云服务](../cloud-services/cloud-services-how-to-configure.md) 虚拟机提供的安全更新和关键更新。 |
| OS 漏洞 |每天分析操作系统配置，确定可能导致虚拟机受攻击的问题。 此策略还建议通过配置更改解决这些漏洞。 请参阅 [建议的基准配置列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) ，详细了解受监视的具体配置。 （目前不对 Windows Server 2016 提供完全支持。） |
| 终结点保护 |建议为所有 Windows 虚拟机预配终结点保护，以便确定并删除病毒、间谍软件以及其他恶意软件。 |
| 磁盘加密 |建议在所有虚拟机中启用磁盘加密，增强静态数据保护。 |
| 网络安全组 |建议配置 [网络安全组](../virtual-network/virtual-networks-nsg.md) ，控制具有公共终结点的 VM 的入站和出站流量。 除非另行指定，否则，为子网配置的网络安全组会被所有虚拟机网络接口继承。 除了查看网络安全组是否已配置，此策略还会评估入站安全规则，确定允许传入流量的规则。 |
| Web 应用程序防火墙 |建议以下之一为 true 时在虚拟机上配置 Web 应用程序防火墙： </br></br>使用了[实例层级公共 IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP)，配置了关联网络安全组的入站安全规则以允许访问端口 80/443。</br></br>使用了负载均衡 IP，配置了关联负载均衡和入站网络地址转换 (NAT) 规则以允许访问端口 80/443。 有关详细信息，请参阅 [Azure Resource Manager 对 负载均衡器的支持](../load-balancer/load-balancer-arm.md)。 |
| 下一代防火墙 |将网络保护扩展到内置到 Azure 中的网络安全组以外。 安全中心会发现那些需要安装下一代防火墙的部署，并允许用户预配虚拟设备。 |
| SQL 审核和威胁检测 |建议允许审核对 Azure 数据库的访问权限，确保合规性；并启用高级威胁检测以便进行调查。 |
| SQL 加密 |建议为 Azure SQL 数据库、关联的备份和事务日志文件启用静态加密。 即使数据被侵犯，入侵者也无法读取这些数据。 |
| 漏洞评估 |建议在 VM 上安装漏洞评估解决方案。 |
| 存储加密 |目前，此功能仅适用于 Azure Blob 和文件。 请注意，在启用存储服务加密以后，只会加密新数据，该存储帐户中的任何现有文件仍会保持不加密状态。 |

配置所有选项以后，在包含建议的“安全策略”边栏选项卡中单击“确定”，然后在包含初始设置的“安全策略”边栏选项卡中单击“保存”。

> [!NOTE]
> 定价层仍适用于资源组级别。 有关详细信息，请访问[定价](https://azure.microsoft.com/pricing/details/security-center/)页。
>
>

## <a name="see-also"></a>另请参阅
在本文档中，已经学习了如何在 Azure 安全中心中配置安全策略。 若要详细了解 Azure 安全中心，请参阅以下内容：

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md)（Azure 安全中心规划和操作指南）。 了解如何规划并理解设计注意事项，以便采用 Azure 安全中心。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理和响应安全警报。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md)（通过 Azure 安全中心监视合作伙伴解决方案）。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。

