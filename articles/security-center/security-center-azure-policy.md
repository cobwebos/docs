---
title: "Azure 安全中心安全策略与 Azure 策略的集成 | Microsoft Docs"
description: "本文档介绍了如何配置 Azure 安全中心安全策略与 Azure 策略的集成。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>在 Azure 策略支持的安全中心设置安全策略
本文档介绍如何在 Azure 策略支持的安全中心配置安全策略，指导用户完成执行此任务所必需的步骤。 


## <a name="how-security-policies-work"></a>安全策略工作原理
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑该策略，也可以使用 [Azure 策略](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)创建新的策略定义、跨管理组（可以代表整个组织、组织中的某个业务部门，等等）分配策略，以及监视策略遵循情况。

> [!NOTE]
> Azure 策略为有限预览版。 单击[此处](https://aka.ms/getpolicy)即可加入。 有关 Azure 策略的详细信息，请阅读 [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)（创建和管理策略以强制要求符合性）。

## <a name="edit-security-policies"></a>编辑安全策略
可以在安全中心为每个 Azure 订阅编辑默认的安全策略。 若要修改安全策略，你必须是该订阅或包含型管理组的所有者或安全管理员。 登录到 Azure 门户，按照后续步骤在安全中心查看安全策略：

1. 在“安全中心”仪表板的“通用”下单击“安全策略”。
2. 选择要在其上启用安全策略的订阅。

    ![策略管理](./media/security-center-policies/security-center-policies-fig10.png)

3. 在“策略组件”部分单击“安全策略”。

    ![策略组件](./media/security-center-policies/security-center-policies-fig12.png)

4. 这是通过 Azure 策略分配给安全中心的默认策略。 可以删除“策略和参数”下的项，也可以在“可用选项”下添加其他策略定义。 为此，可直接单击定义名称旁的加号。

    ![策略定义](./media/security-center-policies/security-center-policies-fig11.png)

5. 如果需要有关策略的更详细说明，请单击该策略，此时会打开另一页面，其中包含详细信息以及具有[策略定义](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure)结构的 JSON 代码：

    ![Json](./media/security-center-policies/security-center-policies-fig14.png)

6. 编辑完后，单击“保存”。


## <a name="available-security-policy-definitions"></a>可用的安全策略定义

请使用下表作为参考，了解默认安全策略中提供的策略定义： 

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
| 存储加密 |目前，此功能仅适用于 Azure Blob 和文件。 在启用存储服务加密以后，只会加密新数据，该存储帐户中的任何现有文件仍会保持不加密状态。 |
| JIT 网络访问 |当启用了恰时访问时，安全中心会通过创建 NSG 规则来锁定发往 Azure VM 的入站流量。 需要选择应锁定 VM 上的哪些端口的入站流量。 有关详细信息，请参阅[使用恰时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |


## <a name="next-step"></a>后续步骤
本文档中已经介绍了如何在安全中心配置安全策略。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md)（Azure 安全中心规划和操作指南）。 了解如何规划并理解设计注意事项，以便采用 Azure 安全中心。
* [Security health monitoring in Azure Security Center](security-center-monitoring.md)（在 Azure 安全中心进行安全运行状况监视）。 了解如何监视 Azure 资源的运行状况。
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）。 了解如何管理和响应安全警报。
* [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md)（通过 Azure 安全中心监视合作伙伴解决方案）。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找有关如何使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
