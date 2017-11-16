---
title: "在 Azure 安全中心设置安全策略 | Microsoft Docs"
description: "本文介绍了如何在 Azure 安全中心配置安全策略。"
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
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: ec5463a785c9afe53ebae558d15027e541a60f6a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>在 Azure 安全中心设置安全策略
本文介绍了如何在安全中心配置安全策略。 

## <a name="how-security-policies-work"></a>安全策略工作原理
安全中心自动为每个 Azure 订阅创建默认的安全策略。 可以在安全中心编辑该策略并监视策略遵循情况。 

> [!NOTE]
> 现在可以使用 Azure 策略（功能受限的预览版）来扩展安全中心策略。 若要加入预览版，请转到 [Sign up for Azure Policy](https://aka.ms/getpolicy)（注册 Azure 策略）。 有关详细信息，请参阅[集成安全中心安全策略与 Azure 策略](security-center-azure-policy.md)。

开发或测试用资源的安全要求可能不同于生产应用型资源。 使用管控数据（例如个人身份信息）的应用程序可能需要更高级别的安全性。 在 Azure 安全中心启用的安全策略可以通过安全建议和监视帮助用户确定可能的漏洞并缓解威胁造成的危害。 若要详细了解如何确定适当的选项，请参阅 [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)。

## <a name="edit-security-policies"></a>编辑安全策略
可以在安全中心为每个 Azure 订阅编辑默认的安全策略。 若要修改安全策略，你必须是订阅的所有者、参与者或安全管理员。 若要配置安全中心的安全策略，请执行以下操作：

1. 登录到 Azure 门户。

2. 在“安全中心”仪表板的“通用”下选择“安全策略”。

3. 选择要为其启用安全策略的订阅。

4. 在“策略组件”部分选择“安全策略”。  
    这是安全中心分配的默认策略。 可以打开或关闭可用的安全建议。

5. 编辑完后，选择“保存”。

## <a name="available-security-policy-definitions"></a>可用的安全策略定义

若要了解默认安全策略中提供的策略定义，请参考下表：

| 策略 | 策略的用途 |
| --- | --- |
| 系统更新 |从 Windows 更新或 Windows Server Update Services 检索包含可用安全更新和关键更新的每日列表。 检索的列表取决于为虚拟机配置的服务，并且会建议用户应用缺少的更新。 对于 Linux 系统，该策略会使用发行版提供的包管理系统确定包含可用更新的包。 此外还会查看是否存在 [Azure 云服务](../cloud-services/cloud-services-how-to-configure-portal.md) 虚拟机提供的安全更新和关键更新。 |
| OS 漏洞 |每天分析操作系统配置，确定可能导致虚拟机受攻击的问题。 此策略还建议通过配置更改解决这些漏洞。 若要详细了解受监视的具体配置，请参阅[建议的基准配置列表](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 （目前不对 Windows Server 2016 提供完全支持。） |
| 终结点保护 |建议为所有 Windows 虚拟机 (VM) 设置终结点保护，以便确定并删除病毒、间谍软件以及其他恶意软件。 |
| 磁盘加密 |建议在所有虚拟机中启用磁盘加密，增强静态数据保护。 |
| 网络安全组 |建议配置 [网络安全组](../virtual-network/virtual-networks-nsg.md) ，控制具有公共终结点的 VM 的入站和出站流量。 除非另行指定，否则，为子网配置的网络安全组会被所有虚拟机网络接口继承。 除了查看网络安全组是否已配置，此策略还会评估入站安全规则，确定允许传入流量的规则。 |
| Web 应用程序防火墙 |建议以下之一为 true 时在虚拟机上设置 Web 应用程序防火墙： <ul><li>使用了[实例层级公共 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)，配置了关联网络安全组的入站安全规则以允许访问端口 80/443。</li><li>使用了负载均衡 IP，配置了关联负载均衡和入站网络地址转换 (NAT) 规则以允许访问端口 80/443。 有关详细信息，请参阅 [Azure 资源管理器对负载均衡器的支持](../load-balancer/load-balancer-arm.md)。</li> |
| 下一代防火墙 |将网络保护扩展到内置到 Azure 中的网络安全组以外。 安全中心会发现那些需要安装下一代防火墙的部署，并允许你设置虚拟设备。 |
| SQL 审核和威胁检测 |建议允许审核对 SQL 数据库的访问权限，确保合规性；并启用高级威胁检测以便进行调查。 |
| SQL 加密 |建议为 SQL 数据库、关联的备份和事务日志文件启用静态加密。 即使数据被侵犯，入侵者也无法读取这些数据。 |
| 漏洞评估 |建议在 VM 上安装漏洞评估解决方案。 |
| 存储加密 |目前，此功能仅适用于 Blob 和 Azure 文件。 在启用存储服务加密以后，只会加密新数据，该存储帐户中的任何现有文件仍会保持不加密状态。 |
| JIT 网络访问 |当启用了实时网络访问时，安全中心会通过创建网络安全组规则来锁定发往 Azure VM 的入站流量。 需要选择应锁定 VM 上的哪些端口的入站流量。 有关详细信息，请参阅[使用恰时功能管理虚拟机访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |


## <a name="next-steps"></a>后续步骤
本文中已经介绍了如何在安全中心配置安全策略。 若要详细了解安全中心，请参阅以下文章：

* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)：学习如何规划和了解有关 Azure 安全中心的设计注意事项。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md)：了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)：了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)：了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)：获取有关使用服务的常见问题的答案。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/):.查找关于 Azure 安全性及合规性的博客文章
