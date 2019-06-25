---
title: 示例-英国官方蓝图-控件映射
description: 控件的英国正式的蓝图示例的映射。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276972"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>控件映射的英国正式的蓝图示例

以下文章详细介绍了英国正式的蓝图示例如何映射到英国官方的控件。
有关控件的详细信息，请参阅[英国官方](https://www.gov.uk/government/publications/government-security-classifications)。

以下映射到所**英国官方**控件。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。   然后，找到并选择 **[预览版]审核英国官方的控制和部署特定的 VM 扩展以支持审核要求**内置策略计划。

## <a name="1-data-in-transit-protection"></a>1 个数据在传输保护

该蓝图通过分配两个 [Azure Policy](../../../policy/overview.md) 定义用于审核与存储帐户和 Redis 缓存建立的不安全连接，来帮助你确保与 Azure 服务之间安全传输信息。

- 应启用仅安全连接到你的 Redis 缓存
- 应启用安全传输到存储帐户

## <a name="23-data-at-rest-protection"></a>2.3 静态数据保护

此蓝图，帮助您强化您的加密控件使用的策略分配 11 [Azure 策略](../../../policy/overview.md)定义来强制实施特定的加密控制和审核使用弱加密设置。
了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略要求加密的 data lake 存储帐户;需要对 SQL 数据库; 透明数据加密审核存储帐户、 SQL 数据库、 虚拟机磁盘和自动化帐户的变量; 缺少加密审核不安全连接到存储帐户和 Redis 缓存中;审核弱的虚拟机密码加密;总结和审核未加密的 Service Fabric 通信。

- 监视 Azure 安全中心中的未加密的 SQL 数据库
- 应在虚拟机上应用磁盘加密
- 自动化帐户的变量应进行加密
- 应启用安全传输到存储帐户
- 应启用安全传输到存储帐户
- Service Fabric 群集都应设置为 EncryptAndSign ClusterProtectionLevel 属性
- 应启用对 SQL 数据库透明数据加密
- 部署 SQL DB 透明数据加密
- 需要对 Data Lake Store 帐户进行加密
- 允许的位置 （已硬编码为"英国南部"和"英国西部"）
- 允许的资源组的位置 （已硬编码为"英国南部"和"英国西部"）

## <a name="52-vulnerability-management"></a>5.2 漏洞管理

此蓝图可帮助你通过将分配五个管理信息系统漏洞[Azure 策略](../../../policy/overview.md)监视缺失的 endpoint protection，缺少系统更新、 操作系统系统漏洞、 SQL 的定义漏洞和虚拟机的漏洞。 这些见解提供有关已部署资源的安全状态的实时信息，可帮助你指定补救措施的优先级。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在计算机上安装系统更新
- 在计算机上的安全配置中的漏洞应采取修正措施
- 在 SQL 数据库上的漏洞应采取修正措施
- 应通过漏洞评估解决方案修正漏洞

## <a name="53-protective-monitoring"></a>5.3 保护监视

此蓝图可帮助你通过将分配保护信息系统资产[Azure 策略](../../../policy/overview.md)定义，用于监视不受限制的存储帐户。 此蓝图还将分配的 Azure 策略定义，监视白名单的活动。

- 审核对存储帐户的不受限的网络访问
- 应在虚拟机上启用自适应应用程序控件
- 在 SQL server 上部署威胁检测
- 适用于 Windows Server 部署默认 Microsoft IaaS 反恶意软件扩展

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 安全用户管理 / 10 标识和身份验证

Azure 实施基于角色的访问控制 (RBAC 向可帮助你管理有权访问的资源在 Azure 中)。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图可帮助你限制并可以通过分配六个控制特权的访问权限[Azure 策略](../../../policy/overview.md)定义要审核的外部帐户的所有者和/或读/写权限和帐户的所有者，读取和/或写入没有启用多重身份验证的权限。

- 应与你的订阅的所有者权限在帐户上启用 MFA
- MFA 应具有写入权限的订阅的已启用的帐户
- 应与你的订阅上的读取权限在帐户上启用 MFA
- 审核具有所有者权限的外部订阅帐户
- 审核具有写入权限的外部订阅帐户
- 审核具有读取权限的外部订阅帐户

此蓝图将分配两个 Azure 策略定义要审核对 SQL 服务器和 Service Fabric 使用 Azure Active Directory 身份验证。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应为 SQL server 预配 Azure Active Directory 管理员
- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证

此蓝图还将分配五个 Azure 策略定义，以审核应优先供审核，其中包括折旧前的帐户和外部帐户的帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图将这两个 Azure 策略定义分配给应考虑删除的审核不建议使用的帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除弃用的帐户拥有所有者权限
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图还将审核 Linux VM 的 Azure 策略定义分配密码的文件权限设置不正确时的警报。 此设计使您可以采取纠正措施以确保身份验证器不会受到影响。

- [预览版]: Audit Linux VM /etc/passwd file permissions are set to 0644

此蓝图可帮助你通过将分配 10 个审核不强制执行最小长度和其他密码要求的 Windows Vm 的 Azure 策略定义强制使用强密码。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- [预览]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [预览]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [预览]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [预览]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [预览]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [预览]: Audit Windows VMs that do not have the password complexity setting enabled
- [预览]: Audit Windows VMs that do not have a maximum password age of 70 days
- [预览]: Audit Windows VMs that do not have a minimum password age of 1 day
- [预览]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [预览]: Audit Windows VMs that allow re-use of the previous 24 passwords

此蓝图还有助于通过分配七个 Azure 策略定义控制对 Azure 资源的访问。 这些策略将审核可能允许更高资源访问权限的资源类型和配置的使用。 了解违反这些策略的资源有助于采取纠正措施来确保仅限已授权的用户访问 Azure 资源。

- [预览]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [预览]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [预览]: Audit Linux VMs that have accounts without passwords
- [预览]: Audit Linux VMs that allow remote connections from accounts without passwords
- 存储帐户应迁移到新的 Azure 资源管理器资源
- 虚拟机应迁移到新的 Azure 资源管理器资源
- 审核不使用托管磁盘的 VM

## <a name="11-external-interface-protection"></a>11 外部界面保护

而不是使用适当的安全用户管理的 25 个以上的策略，此蓝图可帮助你保护免受未经授权的访问服务接口，通过将分配[Azure 策略](../../../policy/overview.md)定义不受限制的监视器存储帐户。 无限制地访问存储帐户可以允许在信息系统内所含的信息进行意外的访问。 此蓝图还将分配一个策略，使虚拟机上的自适应应用程序控件。

- 审核对存储帐户的不受限的网络访问
- 应在虚拟机上启用自适应应用程序控件

## <a name="12-secure-service-administration"></a>12 安全服务管理

Azure 实施基于角色的访问控制 (RBAC 向可帮助你管理有权访问的资源在 Azure 中)。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图可帮助你限制并可以通过分配五个控制特权的访问权限[Azure 策略](../../../policy/overview.md)定义要审核的外部帐户的所有者和/或写入权限和帐户与所有者，和/或写入权限没有启用多重身份验证。

用于云服务管理的系统将具有高特权来访问该服务。 其泄露将产生重大影响，包括绕过安全控制、窃取或操纵大量数据的手段。 服务提供商的管理员用于管理操作的服务的方法应旨在缓解攻击可以破坏服务的安全性的任何风险。 如果不实现这一原则，攻击者可能绕过安全控制、 窃取或操纵大量数据的方法。

- 应与你的订阅的所有者权限在帐户上启用 MFA
- MFA 应具有写入权限的订阅的已启用的帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图将分配两个 Azure 策略定义要审核对 SQL 服务器和 Service Fabric 使用 Azure Active Directory 身份验证。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。

- 应为 SQL server 预配 Azure Active Directory 管理员
- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证

此蓝图还将分配四个 Azure 策略定义，以审核应优先供审核，其中包括折旧前的帐户和使用提升的权限的外部帐户的帐户。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图将这两个 Azure 策略定义分配给应考虑删除的审核不建议使用的帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除弃用的帐户拥有所有者权限
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

此蓝图还将审核 Linux VM 的 Azure 策略定义分配密码的文件权限设置不正确时的警报。 此设计使您可以采取纠正措施以确保身份验证器不会受到影响。

- [预览]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>用户的 13 审核信息

此蓝图可帮助你确保系统事件记录通过分配 6 [Azure 策略](../../../policy/overview.md)审核日志设置 Azure 资源上的定义。 分配的策略还会审核虚拟机是否不向指定的 Log Analytics 工作区发送日志。

- 监视未经审核的 SQL 服务器在 Azure 安全中心
- 审核诊断设置
- 审核 SQL 服务器级别审核设置
- [预览]: Deploy Log Analytics Agent for Linux VMs
- [预览]: Deploy Log Analytics Agent for Windows VMs
- 创建虚拟网络时部署网络观察程序

## <a name="next-steps"></a>后续步骤

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
