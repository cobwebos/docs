---
title: Azure 安全中心安全策略与 Azure 策略的集成 | Microsoft Docs
description: 本文档介绍了如何配置 Azure 安全中心安全策略与 Azure 策略的集成。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: terrylan
ms.openlocfilehash: b3d6d15d41fece613290deb2c77e980caa5dcfef
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018557"
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>集成安全中心安全策略与 Azure 策略
本文可帮助你配置由 [Azure 策略](../azure-policy/azure-policy-introduction.md)提供支持的 Azure 安全中心安全策略。

## <a name="how-security-policies-work"></a>安全策略工作原理
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心内编辑策略，或者使用 Azure 策略执行以下操作：
- 创建新的策略定义。
- 跨管理组和订阅分配策略，这些管理组和订阅可以代表整个组织，或者组织中的某个业务部门。
- 监视策略符合性。

有关 Azure 策略的详细信息，请参阅[创建和管理策略以强制实施符合性](../azure-policy/create-manage-policy.md)。

Azure 策略由以下组件构成：

- **策略**是一个规则
- **计划**是一个策略集合
- **分配**是将计划或策略应用于特定的范围（管理组、订阅或资源组）

资源将根据分配给它的策略进行评估，并且将根据资源符合的策略数收到符合率。

## <a name="edit-security-policies"></a>编辑安全策略
可以在安全中心内为每个 Azure 订阅和管理组编辑默认的安全策略。 若要修改安全策略，你必须是该订阅或包含型管理组的所有者、参与者或安全管理员。 要在安全中心内查看安全策略，请执行以下操作：

1. 在“安全中心”仪表板上，在“策略和符合性”下选择“安全策略”。 此时将打开“策略管理”。

    ![“策略管理”窗格](./media/security-center-azure-policy/security-center-policies-fig10.png)

  “策略管理”显示管理组数、订阅数和工作区数以及管理组结构。

  > [!NOTE]
  > “安全中心”仪表板在“订阅覆盖范围”下显示的订阅数可能会高于在“策略管理”下显示的订阅数。 订阅覆盖范围显示标准订阅、免费订阅和“未覆盖”订阅的数量。 “未覆盖”订阅未启用安全中心，并且不会显示在“策略管理”下。
  >
  >

  表中的列显示了：

 - 策略计划分配 – 分配给订阅或管理组的安全中心内置策略和计划。
 - 符合性 – 管理组、订阅或工作区的总体符合性得分。 得分是各个分配的加权平均值。 加权平均值取决于单个分配中的策略数以及该分配应用于的资源数。

 例如，如果订阅有两台 VM 和一个分配有五个策略的计划，则订阅中有 10 项评估。 如果其中一台 VM 不符合其中的两个策略，则订阅的分配的总体符合性得分为 80%。

 - 覆盖范围 – 标识管理组、订阅或工作区在其中运行的定价层：免费或标准。  若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
 - 设置 – 订阅有“编辑设置”链接。 选择“编辑设置”可以更新订阅设置，例如数据收集、定价层和电子邮件通知。

2. 选择要为其启用安全策略的订阅或管理组。 此时会打开“安全策略”。

3.  在“安全策略”下，选择你希望安全中心监视的控件并通过选择“开启”来提供建议。  如果不希望安全中心监视该控件，请选择“关闭”。

    ![策略组件](./media/security-center-azure-policy/security-policy.png)

4. 选择“保存”。

## <a name="available-security-policy-definitions"></a>可用的安全策略定义

若要了解默认安全策略中提供的策略定义，请参考下表：

| 策略 | 已启用策略的功能 |
| --- | --- |
| 系统更新 |从 Windows 更新或 Windows Server Update Services 检索包含可用安全更新和关键更新的每日列表。 检索的列表取决于为虚拟机配置的服务，并且会建议用户应用缺少的更新。 对于 Linux 系统，该策略会使用发行版提供的包管理系统确定包含可用更新的包。 此外还会查看是否存在 [Azure 云服务](../cloud-services/cloud-services-how-to-configure-portal.md) 虚拟机提供的安全更新和关键更新。 |
| 安全配置 |每天分析操作系统配置，确定可能导致虚拟机受攻击的问题。 此策略还建议通过配置更改解决这些漏洞。 若要详细了解受监视的具体配置，请参阅[建议的基准配置列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 （目前，Windows Server 2016 不完全受支持。） |
| 终结点保护 |建议为所有 Windows 虚拟机 (VM) 设置终结点保护，以便确定并删除病毒、间谍软件以及其他恶意软件。 |
| 磁盘加密 |建议在所有虚拟机中启用磁盘加密，增强静态数据保护。 |
| 网络安全组 |建议配置 [网络安全组](../virtual-network/security-overview.md) ，控制具有公共终结点的 VM 的入站和出站流量。 除非另行指定，否则，为子网配置的网络安全组会被所有虚拟机网络接口继承。 除了查看网络安全组是否已配置，此策略还会评估入站安全规则，确定允许传入流量的规则。 |
| Web 应用程序防火墙 |建议当以下任一项属实时在虚拟机上设置 Web 应用程序防火墙： <ul><li>使用了[实例层级公共 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)，配置了关联网络安全组的入站安全规则以允许访问端口 80/443。</li><li>使用了负载均衡 IP，配置了关联负载均衡和入站网络地址转换 (NAT) 规则以允许访问端口 80/443。 有关详细信息，请参阅 [Azure 资源管理器对负载均衡器的支持](../load-balancer/load-balancer-arm.md)。</li> |
| 下一代防火墙 |将网络保护扩展到内置到 Azure 中的网络安全组以外。 安全中心会发现那些需要安装下一代防火墙的部署，并允许你设置虚拟设备。 |
| SQL 审核和威胁检测 |建议允许审核对 Azure 数据库的访问权限，确保合规性；并启用高级威胁检测以便进行调查。 |
| SQL 加密 |建议为 Azure SQL 数据库、关联的备份和事务日志文件启用静态加密。 即使数据被侵犯，入侵者也无法读取这些数据。 |
| 漏洞评估 |建议在 VM 上安装漏洞评估解决方案。 |
| 存储加密 |目前，此功能仅适用于 Azure Blob 存储和 Azure 文件。 在启用存储服务加密以后，只会加密新数据，该存储帐户中的任何现有文件仍会保持不加密状态。 |
| JIT 网络访问 |当启用了实时网络访问时，安全中心会通过创建网络安全组规则来锁定发往 Azure VM 的入站流量。 需要选择应锁定 VM 上的哪些端口的入站流量。 有关详细信息，请参阅[使用恰时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |

## <a name="management-groups"></a>管理组
如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 Azure 管理组提供订阅上的作用域级别。 可将订阅组织到名为“管理组”的容器中，并将管理策略应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的策略。 为每个目录指定了一个称为“根”管理组的顶级管理组。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 此根管理组允许在目录级别应用全局策略和 RBAC 分配。 若要设置用于 Azure 安全中心的管理组，请根据[为 Azure 安全中心实现租户级可见性](security-center-management-groups.md)一文中的说明进行操作。 

> [!NOTE]
> 务必要了解管理组和订阅的层次结构。 请参阅[使用 Azure 管理组来组织资源](../azure-resource-manager/management-groups-overview.md#root-management-group-for-each-directory)来了解有关管理组、根管理和管理组访问权限的详细信息。
>
>


## <a name="next-steps"></a>后续步骤
本文中已经介绍了如何在安全中心配置安全策略。 若要详细了解安全中心，请参阅以下文章：

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)：学习如何规划和了解有关 Azure 安全中心的设计注意事项。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [为 Azure 安全中心实现租户级可见性](security-center-management-groups.md)：了解如何为 Azure 安全中心设置管理组。 
* [Azure 安全中心常见问题解答](security-center-faq.md)：获取有关使用服务的常见问题的答案。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章

若要了解有关 Azure 策略的详细信息，请参阅[什么是 Azure 策略？](../azure-policy/azure-policy-introduction.md)
