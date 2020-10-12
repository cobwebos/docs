---
title: Azure HDInsight 中的企业安全性常规准则
description: 一些应该可以简化企业安全性套餐的部署和管理的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 8d97886232eecc369746e33df484cbfb9d40da72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87530259"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 中的企业安全性常规信息和准则

部署安全的 HDInsight 群集时，可以遵循一些最佳做法来简化部署和群集管理。 本文介绍了一些常规信息和准则。

## <a name="use-of-secure-cluster"></a>使用安全群集

### <a name="recommended"></a>建议

* 群集将由多个用户同时使用。
* 不同用户对同一数据的访问权限级别不同。

### <a name="not-necessary"></a>不必要

* 你只需运行自动化作业（像单用户帐户一样），使用标准群集就足够了。
* 你可以使用标准群集执行数据导入，在可供用户运行分析作业的不同安全群集上使用同一存储帐户。

## <a name="use-of-local-account"></a>使用本地帐户

* 如果你使用共享用户帐户或本地帐户，则很难确定使用过该帐户更改配置或服务的人员。
* 当用户不再是组织的成员时，使用本地帐户会出现问题。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>策略

* 默认情况下，Ranger 使用“拒绝”作为策略。

* 通过在其中启用了授权的服务进行数据访问时：
  * 会调用 Ranger 授权插件并为其提供请求上下文。
  * Ranger 应用为服务配置的策略。 如果 Ranger 策略失败，则会将访问检查推迟到文件系统。 某些服务（如 MapReduce）仅检查提交请求的用户所拥有的文件/文件夹。 Hive 之类的服务会检查所有权是否匹配或文件系统权限是否适当 (`rwx`)。

* 对于 Hive，用户除了拥有执行创建/更新/删除操作的权限外，还应当对存储上的目录和所有子目录拥有 `rwx` 权限。

* 可以将策略应用于组（首选）而不是个人。

* Ranger 授权程序会针对每个请求评估针对该服务的所有 Ranger 策略。 此评估可能会影响接受作业或查询所耗费的时间。

### <a name="storage-access"></a>存储访问

* 如果存储类型为 WASB，则不涉及任何 OAuth 令牌。
* 如果 Ranger 已执行授权操作，则可使用托管标识进行存储访问。
* 如果 Ranger 未执行任何授权操作，则会使用用户的 OAuth 令牌进行存储访问。

### <a name="hierarchical-name-space"></a>分层命名空间

如果未启用分层命名空间，则：

* 没有继承的权限。
* 能够使用的唯一文件系统权限是**存储数据 XXXX** Azure 角色，该角色直接在 Azure 门户中分配给用户。

### <a name="default-hdfs-permissions"></a>默认的 HDFS 权限

* 默认情况下，用户无权访问 HDFS 上的 **/** 文件夹（需要具有存储 blob 所有者角色才能成功访问）。
* 对于 MapReduce 和其他服务的暂存目录，系统会创建特定于用户的目录，并为其提供 `sticky _wx` 权限。 用户可以在其下创建文件和文件夹，但不能查看其他项目。

### <a name="url-auth"></a>URL 身份验证

如果启用了 URL 身份验证，则：

* 配置将涉及 URL 身份验证中包含哪些前缀（例如 `adl://`）。
* 如果访问权限是针对此 URL 的，则 Ranger 会检查用户是否在允许列表中。
* Ranger 不检查任何细化策略。

## <a name="resource-groups"></a>资源组

为每个群集使用新的资源组，以便区分群集资源。

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG、防火墙和内部网关

* 使用网络安全组 (NSG) 锁定虚拟网络。
* 使用防火墙处理出站访问策略。
* 请使用未向公共 Internet 开放的内部网关。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 是 Microsoft 推出的基于云的标识和访问管理服务。

### <a name="policies"></a>策略

* 禁用使用了基于 IP 地址的策略的条件访问策略。 这要求在部署群集的 VNET 上启用服务终结点。 如果对 MFA 使用外部服务（不同于 AAD 的服务），则基于 IP 地址的策略将不起作用

* 对于联合用户，`AllowCloudPasswordValidation` 策略是必需的。 由于 HDInsight 直接使用用户名/密码从 Azure AD 获取令牌，因此必须为所有联合用户启用此策略。

* 如果需要使用受信任的 IP 来绕过条件访问，请启用服务终结点。

### <a name="groups"></a>组

* 始终通过组来部署群集。
* 使用 Azure AD 来管理组成员身份（比尝试管理群集中的各项服务更轻松）。

### <a name="user-accounts"></a>用户帐户

* 为每个方案使用唯一的用户帐户。 例如，将一个帐户用于导入，将另一个帐户用于查询或其他处理作业。
* 使用基于组的 Ranger 策略，而不是使用个体策略。
* 计划如何管理不应再访问群集的用户。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务

[Azure Active Directory 域服务](../../active-directory-domain-services/overview.md) (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、轻型目录访问协议 (LDAP) 和 Kerberos/NTLM 身份验证。

若要让安全群集加入域，Azure AD DS 是必需的。
HDInsight 不能依赖于本地域控制器或自定义域控制器，因为它引入了太多的故障点、凭据共享、DNS 权限等。 有关详细信息，请参阅 [Azure AD DS 常见问题解答](../../active-directory-domain-services/faqs.md)。

### <a name="azure-ad-ds-instance"></a>Azure AD DS 实例

* 创建使用 `.onmicrosoft.com domain` 的实例。 这样就不会有多个 DNS 服务器为域提供服务。
* 为 LDAPS 创建自签名证书并将其上传到 Azure AD DS。
* 使用对等互连虚拟网络来部署群集（当你有许多团队部署 HDInsight ESP 群集时，这会很有用）。 这可以确保你不需要在具有域控制器的虚拟网络上打开端口 (NSG)。
* 为虚拟网络正确配置 DNS（Azure AD DS 域名应该可以在没有任何 hosts 文件条目的情况下进行解析）。
* 如果要限制出站流量，请确保已通读 [HDInsight 中的防火墙支持](../hdinsight-restrict-outbound-traffic.md)一文

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 同步到 Azure AD DS 的属性

* Azure AD Connect 从本地同步到 Azure AD。
* Azure AD DS 从 Azure AD 同步。

Azure AD DS 定期从 Azure AD 同步对象。 Azure 门户上的 Azure AD DS 边栏选项卡会显示同步状态。 在每个同步阶段，唯一属性可能会在出现冲突后重命名。 请注意从 Azure AD 到 Azure AD DS 的属性映射。

有关详细信息，请参阅 [Azure AD UserPrincipalName 填充](../../active-directory/hybrid/plan-connect-userprincipalname.md)和 [Azure AD DS 同步的工作方式](../../active-directory-domain-services/synchronization.md)。

### <a name="password-hash-sync"></a>密码哈希同步

* 密码的同步与其他对象类型不同。 在 Azure AD 和 Azure AD DS 中，仅会同步不可逆的密码哈希
* 必须通过 AD Connect 启用从本地到 Azure AD 的同步
* 从 Azure AD 到 Azure AD DS 的同步是自动的（延迟在 20 分钟内）。
* 只有在密码已更改的情况下，才会同步密码哈希。 启用密码哈希同步时，所有现有的密码都不会自动同步，因为它们是以不可逆方式存储的。 更改密码时，密码哈希会同步。

### <a name="computer-objects-location"></a>计算机对象位置

每个群集都与单个 OU 相关联。 OU 中预配了一个内部用户。 所有节点都以域加入方式加入到同一个 OU 中。

### <a name="active-directory-administrative-tools"></a>Active Directory 管理工具

有关如何在 Windows Server VM 上安装 Active Directory 管理工具的步骤，请参阅[安装管理工具](../../active-directory-domain-services/tutorial-create-management-vm.md)。

## <a name="troubleshooting"></a>故障排除

### <a name="cluster-creation-fails-repeatedly"></a>群集创建反复失败

最常见原因：

* DNS 配置不正确，群集节点的域加入操作失败。
* NSG 过于严格，阻止加入域。
* 托管标识没有足够的权限。
* 群集名称的前六个字符不唯一（与其他实时群集或已删除群集的相同）。

## <a name="authentication-setup-and-configuration"></a>身份验证设置和配置

### <a name="user-principal-name-upn"></a>用户主体名称 (UPN)

* 请为所有服务使用小写，不过 UPN 在 ESP 群集中不区分大小写
* UPN 前缀应与 Azure AD DS 中的 SAMAccountName 匹配。 不要求与邮件字段匹配。

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari 配置中的 LDAP 属性

有关影响 HDInsight 群集配置的 Ambari 属性的完整列表，请参阅 [Ambari LDAP 身份验证设置](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)。

## <a name="next-steps"></a>后续步骤

* [HDInsight 中带有 Azure Active Directory 域服务的企业安全性套餐配置](./apache-domain-joined-configure-using-azure-adds.md)

* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)。
