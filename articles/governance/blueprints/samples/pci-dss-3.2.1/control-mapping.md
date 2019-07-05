---
title: 示例-PCI DSS v3.2.1 蓝图-控件映射
description: 控件映射到 Azure 策略和 RBAC 的支付卡行业数据安全标准 v3.2.1 蓝图示例。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540952"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>PCI DSS v3.2.1 蓝图示例的控件映射

以下文章详细介绍了如何将 Azure 蓝图 PCI DSS v3.2.1 蓝图示例映射到 PCI DSS v3.2.1 控件。 有关控件的详细信息，请参阅[PCI DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf)。

以下映射到所**PCI DSS v3.2.1:2018**控件。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择 **[预览版]审核 PCI v3.2.1:2018 控制和部署特定的 VM 扩展以支持审核要求**内置策略计划。

## <a name="132-and-134-boundary-protection"></a>1.3.2 和 1.3.4 边界保护

此蓝图可帮助你管理和控制网络通过将分配[Azure 策略](../../../policy/overview.md)监视规则宽松的网络安全组的定义。 过于宽松的规则可能会允许意外的网络访问，应该对其进行评审。 此蓝图分配一个监视未受保护的终结点、 应用程序和存储帐户的 Azure 策略定义。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- 审核对存储帐户的不受限的网络访问
- 应仅限通过面向 Internet 的终结点的访问

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a，4.1，4.1.g、 4.1.h 和 6.5.3 加密保护

此蓝图可帮助你通过将分配强制您使用的加密控件使用的策略[Azure 策略](../../../policy/overview.md)定义强制实施特定的加密控制和审核使用弱加密设置。 了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略要求对; 的 SQL 数据库透明数据加密审核存储帐户和自动化帐户的变量上缺少加密。 还有哪些地址不安全连接到存储帐户、 函数应用、 web 应用、 API 应用和 Redis 缓存中，审核和 Service Fabric 通信未加密的策略。

- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- API 应用应仅可通过 HTTPS
- 监视 Azure 安全中心内未加密的 SQL 数据库
- 应在虚拟机上应用磁盘加密
- 自动化帐户的变量应进行加密
- 应启用仅安全连接到你的 Redis 缓存
- 应启用安全传输到存储帐户
- Service Fabric 群集都应设置为 EncryptAndSign ClusterProtectionLevel 属性
- 应启用对 SQL 数据库透明数据加密
- 部署 SQL DB 透明数据加密

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1、 6.2、 6.6 和 11.2.1 漏洞扫描和系统更新

此蓝图可帮助你通过将分配管理信息系统漏洞[Azure 策略](../../../policy/overview.md)监视缺少系统更新、 操作系统漏洞、 SQL 漏洞和虚拟机的定义Azure 安全中心中的漏洞。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 适用于 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展
- 部署 SQL Server 上的威胁检测
- 应在计算机上安装系统更新
- 在计算机上的安全配置中的漏洞应采取修正措施
- 在 SQL 数据库上的漏洞应采取修正措施
- 应通过漏洞评估解决方案修正漏洞

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 及 7.1.3 职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助您维护通过分配适当数量的 Azure 订阅所有者[Azure 策略](../../../policy/overview.md)审核的 Azure 订阅的所有者数量的定义。 管理订阅所有者权限有助于实现适当的职责分离。

- 应该有多个所有者分配给你的订阅
- 最多 3 个所有者应被指定为你的订阅 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2、 7.2.1，8.3.1.a、 8.3.1.b 的管理特权的访问权限

此蓝图可帮助你限制并可以通过将分配控制特权的访问权限[Azure 策略](../../../policy/overview.md)定义要审核的外部帐户的所有者，编写和/或读取权限和员工帐户所有者和/或写入没有启用多重身份验证的权限。 Azure 实施基于角色的访问控制 (RBAC) 来管理谁有权访问 Azure 资源。 了解实施自定义 RBAC 规则的位置有助于验证需求以及实施是否适当，因为自定义 RBAC 规则容易出错。 此蓝图还将分配[Azure 策略](../../../policy/overview.md)定义要审核的 SQL Server 的 Azure Active Directory 身份验证使用。 使用 Azure Active Directory 身份验证简化权限管理并集中的数据库用户和其他 Microsoft 标识管理  
服务。
 
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户
- 应与你的订阅的所有者权限在帐户上启用 MFA
- MFA 应具有写入权限的订阅的已启用的帐户
- 应与你的订阅上的读取权限在帐户上启用 MFA
- 应为 SQL server 预配 Azure Active Directory 管理员
- 审核自定义 RBAC 规则的使用

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 和 8.1.5 最小权限和查看用户的访问权限

Azure 实施基于角色的访问控制 (RBAC 向可帮助你管理有权访问的资源在 Azure 中)。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图分配[Azure 策略](../../../policy/overview.md)定义要审核应优先供审核，其中包括折旧前的帐户和使用提升的权限的外部帐户的帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除弃用的帐户拥有所有者权限
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应从订阅中删除拥有读取权限的外部帐户

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 删除或调整的访问权限

Azure 到 Azure 中的资源实施基于角色的访问控制 (RBAC) 可帮助您管理谁有权访问。 使用 Azure Active Directory 和 RBAC，可以更新用户角色，以反映组织的更改。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图分配[Azure 策略](../../../policy/overview.md)定义要审核折旧前应考虑删除的帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除弃用的帐户拥有所有者权限

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b、 8.2.4.a,b 和 8.2.5 基于密码的身份验证

此蓝图可帮助你通过将分配强制使用强密码[Azure 策略](../../../policy/overview.md)审核不强制执行最小长度和其他密码要求的 Windows Vm 的定义。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- [预览版]: Audit Windows VMs that do not have a maximum password age of 70 days
- [预览]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [预览]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [预览]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [预览]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [预览]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 和 10.5.4 审核生成

此蓝图可帮助您确保通过将分配记录系统事件[Azure 策略](../../../policy/overview.md)审核日志设置 Azure 资源上的定义。
诊断日志针对 Azure 资源中执行的操作提供见解。 Azure 日志依赖于同步的内部时钟创建各个资源中事件的时间相关记录。

- 监视未经审核的 SQL 服务器在 Azure 安全中心
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- 在 SQL server 上部署审核
- 存储帐户应迁移到新的 Azure 资源管理器资源
- 虚拟机应迁移到新的 Azure 资源管理器资源

## <a name="1236-and-1237-information-security"></a>12.3.6 和 12.3.7 信息安全

此蓝图可帮助你管理和控制你的网络，通过将分配[Azure 策略](../../../policy/overview.md)环境允许审核的可接受的网络位置和已批准的公司产品的定义。 这些是可自定义的每个公司通过在每个这些策略的策略参数。

- 允许的位置
- 允许的资源组的位置

## <a name="next-steps"></a>后续步骤

现在，你已经查看过的 PCI DSS v3.2.1 蓝图控件映射，请访问以下文章，了解概述以及如何部署此示例：

> [!div class="nextstepaction"]
> [PCI DSS v3.2.1 蓝图-概述](./index.md)
> [PCI DSS v3.2.1 蓝图-部署步骤](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
