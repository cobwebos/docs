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
ms.openlocfilehash: 045cf83caa15cb2487b4781f3c1f42752880580c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>集成安全中心安全策略与 Azure 策略
本文介绍如何配置 Azure 策略所支持的 Azure 安全中心安全策略。 

## <a name="how-security-policies-work"></a>安全策略工作原理
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑策略，也可以使用 [Azure 策略](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)执行以下操作：
* 创建新的策略定义。
* 跨管理组分配策略，这些管理组可以代表整个组织，或者组织中的某个业务部门。
* 监视策略符合性。

> [!NOTE]
> Azure 策略为有限预览版。 若要加入，请转到 [Sign up for Azure Policy](https://aka.ms/getpolicy)（注册 Azure 策略）。 有关 Azure 策略的详细信息，请参阅[创建和管理策略以强制实施符合性](http://docs.microsoft.com/azure/azure-policy/create-manage-policy)。

## <a name="edit-security-policies"></a>编辑安全策略
可以在安全中心为每个 Azure 订阅编辑默认的安全策略。 若要修改安全策略，你必须是该订阅或包含型管理组的所有者、参与者或安全管理员。 若要查看安全中心的安全策略，请执行以下操作：

1. 登录到 Azure 门户。

2. 在“安全中心”仪表板的“通用”下选择“安全策略”。

    ![“策略管理”窗格](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. 选择要为其启用安全策略的订阅。  

4. 在“策略组件”部分选择“安全策略”。  
    此时会打开“基本信息”窗口。

    ![策略组件](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. 若要删除策略定义，请在“策略和参数”下要删除的定义旁边选择“删除”。

6. 单击“保存” 。  
    此时会打开“可用定义”窗口，其中显示通过 Azure 策略分配到安全中心的默认策略。 

7. （可选）在“可用定义”窗口中，执行以下操作之一：

    * 若要添加策略定义，请选择定义旁边的加号 (+)。

    ![可用策略定义](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * 如需策略的详细说明，请选择它。  
    此时会打开定义“预览”窗口。 该窗口会显示对定义的说明，以及 JSON 代码的链接，其中提供了[策略定义](../azure-policy/policy-definition.md)结构。

    ![定义“预览”窗口](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. 编辑完后，选择“保存”。

## <a name="available-security-policy-definitions"></a>可用的安全策略定义

若要了解默认安全策略中提供的策略定义，请参考下表： 

| 策略 | 已启用策略的功能 |
| --- | --- |
| 系统更新 |从 Windows 更新或 Windows Server Update Services 检索包含可用安全更新和关键更新的每日列表。 检索的列表取决于为虚拟机配置的服务，并且会建议用户应用缺少的更新。 对于 Linux 系统，该策略会使用发行版提供的包管理系统确定包含可用更新的包。 此外还会查看是否存在 [Azure 云服务](../cloud-services/cloud-services-how-to-configure-portal.md) 虚拟机提供的安全更新和关键更新。 |
| OS 漏洞 |每天分析操作系统配置，确定可能导致虚拟机受攻击的问题。 此策略还建议通过配置更改解决这些漏洞。 若要详细了解受监视的具体配置，请参阅[建议的基准配置列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 （目前不对 Windows Server 2016 提供完全支持。） |
| 终结点保护 |建议为所有 Windows 虚拟机 (VM) 设置终结点保护，以便确定并删除病毒、间谍软件以及其他恶意软件。 |
| 磁盘加密 |建议在所有虚拟机中启用磁盘加密，增强静态数据保护。 |
| 网络安全组 |建议配置 [网络安全组](../virtual-network/virtual-networks-nsg.md) ，控制具有公共终结点的 VM 的入站和出站流量。 除非另行指定，否则，为子网配置的网络安全组会被所有虚拟机网络接口继承。 除了查看网络安全组是否已配置，此策略还会评估入站安全规则，确定允许传入流量的规则。 |
| Web 应用程序防火墙 |建议以下之一为 true 时在虚拟机上设置 Web 应用程序防火墙： <ul><li>使用了[实例层级公共 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)，配置了关联网络安全组的入站安全规则以允许访问端口 80/443。</li><li>使用了负载均衡 IP，配置了关联负载均衡和入站网络地址转换 (NAT) 规则以允许访问端口 80/443。 有关详细信息，请参阅 [Azure 资源管理器对负载均衡器的支持](../load-balancer/load-balancer-arm.md)。</li> |
| 下一代防火墙 |将网络保护扩展到内置到 Azure 中的网络安全组以外。 安全中心会发现那些需要安装下一代防火墙的部署，并允许你设置虚拟设备。 |
| SQL 审核和威胁检测 |建议允许审核对 Azure 数据库的访问权限，确保合规性；并启用高级威胁检测以便进行调查。 |
| SQL 加密 |建议为 Azure SQL 数据库、关联的备份和事务日志文件启用静态加密。 即使数据被侵犯，入侵者也无法读取这些数据。 |
| 漏洞评估 |建议在 VM 上安装漏洞评估解决方案。 |
| 存储加密 |目前，此功能仅适用于 Azure Blob 存储和 Azure 文件。 在启用存储服务加密以后，只会加密新数据，该存储帐户中的任何现有文件仍会保持不加密状态。 |
| JIT 网络访问 |当启用了实时网络访问时，安全中心会通过创建网络安全组规则来锁定发往 Azure VM 的入站流量。 需要选择应锁定 VM 上的哪些端口的入站流量。 有关详细信息，请参阅[使用恰时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |


## <a name="next-steps"></a>后续步骤
本文中已经介绍了如何在安全中心配置安全策略。 若要详细了解安全中心，请参阅以下文章：

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)：学习如何规划和了解有关 Azure 安全中心的设计注意事项。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)：获取有关使用服务的常见问题的答案。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章
