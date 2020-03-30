---
title: Azure HDInsight 中的企业安全一般准则
description: 一些最佳做法，应该使企业安全包的部署和管理更容易。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463201"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企业安全一般信息和指南

部署安全 HDInsight 群集时，有一些最佳做法应使部署和群集管理更加轻松。 这里讨论了一些一般性信息和准则。

## <a name="use-of-secure-cluster"></a>使用安全群集

### <a name="recommended"></a>建议

* 群集将由多个用户同时使用。
* 用户对相同数据具有不同级别的访问权限。

### <a name="not-necessary"></a>不必要

* 您将只运行自动作业（如单个用户帐户），标准群集足够好。
* 您可以使用标准群集执行数据导入，并在用户可以运行分析作业的不同安全群集上使用相同的存储帐户。

## <a name="use-of-local-account"></a>使用本地帐户

* 如果您使用共享用户帐户或本地帐户，则很难确定谁使用该帐户来更改配置或服务。
* 当用户不再是组织的一部分时，使用本地帐户存在问题。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>策略

* 默认情况下，游侠使用**拒绝**作为策略。

* 通过启用授权的服务进行数据访问：
  * 将调用 Ranger 授权插件，并给出请求的上下文。
  * 游侠应用为服务配置的策略。 如果 Ranger 策略失败，访问检查将延迟到文件系统。 某些服务（如 MapReduce）仅检查提交请求的同一用户是否拥有该文件/文件夹。 服务（如 Hive），检查所有权匹配或适当的文件系统权限 （）。`rwx`

* 对于 Hive，除了具有执行"创建/更新/删除"权限的权限外，用户还应对存储`rwx`目录和所有子目录具有权限。

* 策略可以应用于组（首选），而不是个人。

* 游侠授权者将为每个请求评估该服务的所有游侠策略。 此评估可能会影响接受作业或查询所花的时间。

### <a name="storage-access"></a>存储访问

* 如果存储类型为 WASB，则不涉及 OAuth 令牌。
* 如果 Ranger 已执行授权，则存储访问将使用托管标识进行。
* 如果 Ranger 未执行任何授权，则存储访问将使用用户的 OAuth 令牌进行。

### <a name="hierarchical-name-space"></a>分层名称空间

未启用中的分层名称空间时：

* 没有继承的权限。
* 只有有效的文件系统权限是**存储数据 XXXX** RBAC 角色，该角色将直接分配给 Azure 门户中的用户。

### <a name="default-hdfs-permissions"></a>默认 HDFS 权限

* 默认情况下，用户无法访问 HDFS 上的**/** 文件夹（他们需要位于存储 Blob 所有者角色中才能成功访问）。
* 对于 mapreduce 和其他暂存目录，将创建特定于用户的目录并提供`sticky _wx`权限。 用户可以在下面创建文件和文件夹，但不能查看其他项目。

### <a name="url-auth"></a>URL auth

如果启用了 URL auth：

* 配置将包含 URL auth（如`adl://`） 中涵盖的前缀。
* 如果访问是此 URL，则 Ranger 将检查用户是否位于允许列表中。
* 游侠不会检查任何细粒度策略。

## <a name="resource-groups"></a>资源组

为每个群集使用新的资源组，以便区分群集资源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG、防火墙和内部网关

* 使用网络安全组 （NSG） 锁定虚拟网络。
* 使用防火墙处理出站访问策略。
* 使用不对公众互联网开放的内部网关。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure 活动目录](../../active-directory/fundamentals/active-directory-whatis.md)（Azure AD） 是 Microsoft 基于云的标识和访问管理服务。

### <a name="policies"></a>策略

* 使用基于 IP 地址的策略禁用条件访问策略。 这要求在部署群集的 VNET 上启用服务终结点。 如果您为 MFA 使用外部服务（AAD 以外的服务），则基于 IP 地址的策略将不起作用

* `AllowCloudPasswordValidation`联合用户需要策略。 由于 HDInsight 直接使用用户名/密码从 Azure AD 获取令牌，因此必须为所有联合用户启用此策略。

* 如果需要使用受信任的 IP 进行条件访问绕过，则启用服务终结点。

### <a name="groups"></a>组

* 始终使用组部署群集。
* 使用 Azure AD 管理组成员身份（比尝试管理群集中的单个服务更容易）。

### <a name="user-accounts"></a>用户帐户

* 为每个方案使用唯一的用户帐户。 例如，使用帐户导入，使用另一个帐户进行查询或其他处理作业。
* 使用基于组的游侠策略而不是单个策略。
* 制定计划，了解如何管理不应再访问群集的用户。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务

[Azure 活动目录域服务](../../active-directory-domain-services/overview.md)（Azure AD DS） 提供托管域服务，如域联接、组策略、轻量级目录访问协议 （LDAP） 和与 Windows 服务器活动目录完全兼容的 Kerberos / NTLM 身份验证。

安全群集需要 Azure AD DS 才能加入域。
HDInsight 不能依赖于本地域控制器或自定义域控制器，因为它引入了太多的故障点、凭据共享、DNS 权限等。 有关详细信息，请参阅[Azure AD DS 常见问题解答](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 实例

* 使用 创建`.onmicrosoft.com domain`实例。 这样，不会有多个 DNS 服务器为域提供服务。
* 为 LDAPS 创建自签名证书并将其上载到 Azure AD DS。
* 使用对等虚拟网络部署群集（当您有许多团队部署 HDInsight ESP 群集时，这将很有帮助）。 这可确保无需使用域控制器在虚拟网络上打开端口 （NSG）。
* 正确配置虚拟网络的 DNS（Azure AD DS 域名应在没有任何主机文件条目的情况下解析）。
* 如果要限制出站流量，请确保已通读[HDInsight 中的防火墙支持](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 同步到 Azure AD DS 的属性

* Azure AD 将同步从本地连接到 Azure AD。
* Azure AD DS 从 Azure AD 同步。

Azure AD DS 会定期同步来自 Azure AD 的对象。 Azure 门户上的 Azure AD DS 边栏选项卡显示同步状态。 在同步的每个阶段，唯一属性可能会发生冲突并重命名。 注意从 Azure AD 到 Azure AD DS 的属性映射。

有关详细信息，请参阅[Azure AD 用户主名称填充](../../active-directory/hybrid/plan-connect-userprincipalname.md)，以及[Azure AD DS 同步的工作原理](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密码哈希同步

* 密码与其他对象类型的同步方式不同。 在 Azure AD 和 Azure AD DS 中仅同步不可逆的密码哈希
* 必须通过 AD 连接启用到 Azure AD 的本地
* Azure AD 到 Azure AD DS 同步是自动的（延迟时间不到 20 分钟）。
* 密码哈希仅在密码更改时同步。 启用密码哈希同步时，所有现有密码不会自动同步，因为它们是不可逆的存储。 更改密码时，密码哈希将同步。

### <a name="computer-objects-location"></a>计算机对象位置

每个群集都与单个 OU 相关联。 内部用户在 OU 中预配。 所有节点都加入到同一 OU 中的域。

### <a name="active-directory-administrative-tools"></a>活动目录管理工具

有关如何在 Windows 服务器 VM 上安装活动目录管理工具的步骤，请参阅[安装管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="cluster-creation-fails-repeatedly"></a>群集创建重复失败

最常见的原因：

* DNS 配置不正确，群集节点的域联接失败。
* NSG 太严格，阻止域加入。
* 托管标识没有足够的权限。
* 群集名称在前六个字符（与另一个实时群集或已删除的群集中）上并不是唯一的。

## <a name="next-steps"></a>后续步骤

* [HDInsight 中具有 Azure 活动目录域服务的企业安全包配置](./apache-domain-joined-configure-using-azure-adds.md)

* [将 Azure 活动目录用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)。
