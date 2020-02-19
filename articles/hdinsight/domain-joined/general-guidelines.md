---
title: Azure HDInsight 中的企业安全一般准则
description: 一些最佳实践应使企业安全性套餐部署和管理更容易。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463201"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企业安全一般信息和指南

部署安全的 HDInsight 群集时，有一些最佳实践应该使部署和群集管理更容易。 此处介绍了一些常规信息和指导原则。

## <a name="use-of-secure-cluster"></a>使用安全群集

### <a name="recommended"></a>建议

* 多个用户将同时使用该群集。
* 用户对同一数据具有不同的访问级别。

### <a name="not-necessary"></a>无需

* 只运行自动作业（例如单用户帐户），标准群集就足够了。
* 你可以使用标准群集执行数据导入，并在用户可在其中运行分析作业的不同安全群集上使用相同的存储帐户。

## <a name="use-of-local-account"></a>使用本地帐户

* 如果你使用共享用户帐户或本地帐户，则很难确定使用该帐户更改配置或服务的人员。
* 当用户不再是组织的一部分时，使用本地帐户会出现问题。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>策略

* 默认情况下，Ranger 使用**Deny**作为策略。

* 通过启用了授权的服务进行数据访问时：
  * 在请求的上下文中调用 Ranger authorization 插件。
  * Ranger 应用为服务配置的策略。 如果 Ranger 策略失败，则会将访问检查推迟到文件系统。 某些服务（如 MapReduce）仅检查提交请求的同一用户所拥有的文件/文件夹。 Hive 等服务，检查所有权是否匹配或相应的文件系统权限（`rwx`）。

* 对于 Hive，除了具有创建/更新/删除权限的权限外，用户还应具有对存储和所有子目录中的目录的 `rwx`权限。

* 可以将策略应用于组（首选），而不是个人。

* Ranger 授权者将评估针对每个请求的该服务的所有 Ranger 策略。 此评估可能会影响接受作业或查询所需的时间。

### <a name="storage-access"></a>存储访问

* 如果存储类型为 WASB，则不涉及任何 OAuth 令牌。
* 如果 Ranger 已执行授权，则使用托管标识进行存储访问。
* 如果 Ranger 未执行任何授权，则使用用户的 OAuth 令牌进行存储访问。

### <a name="hierarchical-name-space"></a>分层命名空间

如果未启用分层命名空间：

* 没有继承权限。
* 只有工作文件系统权限是**存储数据 XXXX** RBAC 角色，才能直接在 Azure 门户中分配给用户。

### <a name="default-hdfs-permissions"></a>默认 HDFS 权限

* 默认情况下，用户无法访问 HDFS 上的 **/** 文件夹（他们需要在存储 blob 所有者角色中才能成功访问）。
* 对于 mapreduce 和其他的暂存目录，将创建用户特定的目录，并 `sticky _wx` 权限提供该目录。 用户可以在其下创建文件和文件夹，但不能查看其他项目。

### <a name="url-auth"></a>URL 身份验证

如果启用了 url 身份验证：

* 此配置将包含 url 身份验证中包含的前缀（如 `adl://`）。
* 如果此 url 的访问权限，则 Ranger 将检查用户是否在允许列表中。
* Ranger 不检查任何精细的策略。

## <a name="resource-groups"></a>资源组

为每个群集使用新的资源组，以便可以区分群集资源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>Nsg、防火墙和内部网关

* 使用网络安全组（Nsg）锁定虚拟网络。
* 使用防火墙处理出站访问策略。
* 使用未开放到公共 internet 的内部网关。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) （Azure AD）是 Microsoft 的基于云的标识和访问管理服务。

### <a name="policies"></a>策略

* 使用基于 IP 地址的策略禁用条件性访问策略。 这要求在部署群集的 Vnet 上启用服务终结点。 如果为 MFA 使用外部服务（AAD 除外），则基于 IP 地址的策略将不起作用

* 联合用户需要 `AllowCloudPasswordValidation` 策略。 由于 HDInsight 直接使用用户名/密码从 Azure AD 获取令牌，因此必须为所有联合用户启用此策略。

* 如果需要使用受信任的 Ip 进行条件访问绕过，请启用服务终结点。

### <a name="groups"></a>组

* 始终使用组部署群集。
* 使用 Azure AD 来管理组成员身份（比尝试管理群集中的各个服务更轻松）。

### <a name="user-accounts"></a>用户帐户

* 为每个方案使用唯一的用户帐户。 例如，使用帐户进行导入，将另一个帐户用于查询或其他处理作业。
* 使用基于组的 Ranger 策略，而不是单个策略。
* 计划如何管理不应再访问群集的用户。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务

[Azure Active Directory 域服务](../../active-directory-domain-services/overview.md)（Azure AD DS）提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、轻型目录访问协议（LDAP）和 KERBEROS/NTLM 身份验证。

若要使安全群集加入域，Azure AD DS 是必需的。
HDInsight 不能依赖于本地域控制器或自定义域控制器，因为它引入了太多的故障点、凭据共享、DNS 权限等。 有关详细信息，请参阅[AZURE AD DS faq](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 实例

* 创建具有 `.onmicrosoft.com domain`的实例。 这样一来，就不会有多个 DNS 服务器为域提供服务。
* 为 LDAPS 创建自签名证书并将其上传到 Azure AD DS。
* 使用对等互连虚拟网络部署群集（当有许多团队部署 HDInsight ESP 群集时，这将很有帮助）。 这可以确保不需要在具有域控制器的虚拟网络上打开端口（Nsg）。
* 为虚拟网络正确配置 DNS （Azure AD DS 域名应解析为不包含任何主机文件条目）。
* 如果要限制出站流量，请确保已通读[HDInsight 中的防火墙支持](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 同步到 Azure AD DS 的属性

* Azure AD 将同步从本地连接到 Azure AD。
* 从 Azure AD Azure AD DS 同步。

Azure AD DS 会定期同步 Azure AD 的对象。 Azure 门户上的 "Azure AD DS" 边栏选项卡显示同步状态。 在每个同步阶段，唯一属性可能会出现冲突，并已重命名。 请注意从 Azure AD 到 Azure AD DS 的属性映射。

有关详细信息，请参阅[Azure AD UserPrincipalName 填充](../../active-directory/hybrid/plan-connect-userprincipalname.md)和[Azure AD DS 同步的工作原理](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密码哈希同步

* 密码的同步与其他对象类型不同。 仅在 Azure AD 和 Azure AD DS 中同步非可逆密码哈希
* 必须通过 AD Connect 启用本地到 Azure AD
* Azure AD 为 Azure AD DS 同步为自动（延迟在20分钟内）。
* 仅当存在更改的密码时，才会同步密码哈希。 启用密码哈希同步时，所有现有密码都不会自动同步，因为它们存储在不可逆转中。 更改密码时，密码哈希将同步。

### <a name="computer-objects-location"></a>计算机对象位置

每个群集都与单个 OU 关联。 在 OU 中预配了内部用户。 所有节点都加入到同一个 OU 中。

### <a name="active-directory-administrative-tools"></a>Active Directory 管理工具

有关如何在 Windows Server VM 上安装 Active Directory 管理工具的步骤，请参阅[安装管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>故障排除

### <a name="cluster-creation-fails-repeatedly"></a>群集创建重复失败

最常见的原因：

* DNS 配置不正确，群集节点的域加入失败。
* Nsg 过于严格，无法加入域。
* 托管标识没有足够的权限。
* 群集名称在前六个字符上不唯一（使用其他活动群集或已删除的群集）。

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中企业安全性套餐配置与 Azure Active Directory 域服务](./apache-domain-joined-configure-using-azure-adds.md)

* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)。
