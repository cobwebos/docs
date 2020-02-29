---
title: Azure AD DS 的企业安全性-Azure HDInsight
description: 了解如何使用 Azure Active Directory 域服务设置和配置 HDInsight 企业安全性套餐群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196852"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>在 HDInsight 中企业安全性套餐配置与 Azure Active Directory 域服务

企业安全性套餐（ESP）群集在 Azure HDInsight 群集上提供多用户访问权限。 具有 ESP 的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。

本文介绍如何使用 Azure Active Directory 域服务（Azure AD DS）配置使用 ESP 的 HDInsight 群集。

> [!NOTE]  
> ESP 在 HDInsight 3.6 和4.0 中已正式发布，适用于以下群集类型： Apache Spark、交互式、Hadoop 和 HBase。 Apache Kafka 群集类型的 ESP 仅限预览，并且仅支持最大努力。 在 ESP GA 日期（2018年10月1日）之前创建的 ESP 群集不受支持。

## <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

> [!NOTE]  
> 只有租户管理员有权启用 Azure AD DS。 如果群集存储 Azure Data Lake Storage Gen1 或 Gen2，则必须仅针对将需要使用基本 Kerberos 身份验证来访问群集的用户禁用 Azure 多重身份验证。 
>
> 你可以使用 "[受信任的 ip](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) " 或 "[条件性访问](../../active-directory/conditional-access/overview.md)"，仅在访问 HDInsight 群集的虚拟网络的 IP 范围时*才*为特定用户禁用多重身份验证。 如果使用条件访问，请确保已在 HDInsight 虚拟网络上启用 Active Directory 服务终结点。
>
> 如果群集存储是 Azure Blob 存储，请不要禁用多重身份验证。

启用 Azure AD DS 是创建具有 ESP 的 HDInsight 群集之前的先决条件。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。 

启用 Azure AD DS 后，默认情况下，所有用户和对象开始从 Azure Active Directory （Azure AD）同步到 Azure AD DS。 同步操作的时长取决于 Azure AD 中对象的数目。 对于几百个对象，同步可能需要几天。 

用于 Azure AD DS 的域名必须是39个字符或更少，才能与 HDInsight 配合使用。

你可以选择只同步需要访问 HDInsight 群集的组。 这种仅同步特定组的选项称为“范围有限的同步”。 有关说明，请参阅[将范围内的同步从 Azure AD 配置到托管域](../../active-directory-domain-services/scoped-synchronization.md)。

启用安全 LDAP 时，请将域名置于证书中的 "使用者名称" 和 "使用者备用名称"。 例如，如果域名为 *contoso100.onmicrosoft.com*，请确保证书所有者名称和使用者可选名称中存在完全匹配的名称。 有关详细信息，请参阅[为 Azure AD DS 托管域配置安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。 

下面的示例创建一个自签名证书。 域名*contoso100.onmicrosoft.com*同时位于 `Subject` （使用者名称）和 `DnsName` （使用者备用名称）中。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>检查 Azure AD DS 运行状态
通过选择 "**管理**" 类别中的 "**运行状况**"，查看 Azure Active Directory 域服务的运行状况状态。 请确保 Azure AD DS 的状态为绿色（正在运行），并且同步已完成。

![Azure AD DS 运行状况](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>创建并授权托管标识

您可以使用*用户分配的托管标识*来简化和帮助保护域服务操作。 将**HDInsight 域服务参与者**角色分配给托管标识时，它可以读取、创建、修改和删除域服务操作。 

HDInsight 企业安全性套餐需要某些域服务操作（例如创建 Ou 和服务主体）。 可以在任何订阅中创建托管标识。 有关一般托管标识的详细信息，请参阅[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 有关 Azure HDInsight 中托管标识的工作方式的详细信息，请参阅[Azure hdinsight 中的托管标识](../hdinsight-managed-identities.md)。

若要设置 ESP 群集，请创建用户分配的托管标识（如果尚未安装）。 有关说明，请参阅[创建、列出、删除或使用 Azure 门户向用户分配的托管标识分配角色](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

接下来，将**HDInsight 域服务参与者**角色分配给 Azure AD DS 的**访问控制**中的托管标识。 需要 Azure AD DS 管理员权限才能进行此角色分配。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

分配**HDInsight 域服务参与者**角色可确保此标识具有对 Azure AD DS 域上的域服务操作的适当（代表）访问权限。 这些操作包括创建和删除 Ou。

创建托管标识并为其指定了正确的角色后，Azure AD DS 管理员可以设置可以使用此托管标识的用户。 首先，管理员在门户中选择托管标识，然后选择 "**概述**" 下的 "**访问控制（IAM）** "。 然后，在右侧，管理员将**管理的标识操作员**角色分配给要创建 HDInsight ESP 群集的用户或组。 

例如，Azure AD DS 管理员可以为**sjmsi**托管标识将此角色分配给**MarketingTeam**组，如下图所示。 此分配可确保组织中的适当人员可以使用托管标识创建 ESP 群集。

![HDInsight 托管标识操作者角色分配](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>网络注意事项

> [!NOTE]  
> 必须在基于 Azure 资源管理器的虚拟网络中部署 Azure AD DS。 Azure AD DS 不支持经典虚拟网络。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

启用 Azure AD DS 后，本地域名系统（DNS）服务器将在 Active Directory 虚拟机（Vm）上运行。 将 Azure AD DS 虚拟网络配置为使用这些自定义 DNS 服务器。 若要查找正确的 IP 地址，请在 "**管理**" 类别中选择 "**属性**"，然后在 "**虚拟网络"** 下的 "IP 地址"

![查找本地 DNS 服务器的 IP 地址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

通过选择 "**设置**" 类别中的 " **dns 服务器**"，将 Azure AD DS 虚拟网络中的 dns 服务器的配置更改为使用这些自定义 ip。 然后选择 "**自定义**" 选项，在文本框中输入第一个 IP 地址，然后选择 "**保存**"。 使用相同的步骤添加更多 IP 地址。

![正在更新虚拟网络 DNS 配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

将 Azure AD DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果计划使用不同的虚拟网络，则必须对这些虚拟网络对等互连，以便域控制器对 HDInsight Vm 可见。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。 

对等互连虚拟网络后，将 HDInsight 虚拟网络配置为使用自定义 DNS 服务器，并输入 Azure AD DS 专用 Ip 作为 DNS 服务器地址。 当两个虚拟网络都使用相同的 DNS 服务器时，你的自定义域名将解析为正确的 IP，并且可从 HDInsight 访问。 例如，如果你的域名是 `contoso.com`的，那么在此步骤之后，`ping contoso.com` 应解析为正确的 Azure AD DS IP。

![为对等互连虚拟网络配置自定义 DNS 服务器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果你在 HDInsight 子网中使用网络安全组（NSG）规则，则应该允许入站和出站流量[所需的 ip](../hdinsight-management-ip-addresses.md) 。

若要测试网络是否设置正确，请将 Windows VM 加入 HDInsight 虚拟网络/子网，并对域名进行 ping 操作。 （应将其解析为 IP。）运行**ldp.exe**以访问 Azure AD DS 域。 然后，将此 Windows VM 加入到域中，以确认客户端和服务器之间的所有必需 RPC 调用都成功。 

你还可以使用**nslookup**来确认对你的存储帐户或你可能使用的任何外部数据库（例如，external Hive 元存储或 Ranger DB）的网络访问。 如果 NSG 帮助保护 Azure AD DS，请确保在 Azure AD DS 子网的 NSG 规则中允许所有[所需的端口](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)。 如果此 Windows VM 的域加入成功，则可以继续执行下一步，并创建 ESP 群集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>使用 ESP 创建 HDInsight 群集

正确设置前面的步骤后，下一步是创建启用了 ESP 的 HDInsight 群集。 创建 HDInsight 群集时，可以在 "**安全 + 网络**" 选项卡上启用企业安全性套餐。如果希望使用 Azure 资源管理器模板进行部署，请使用门户体验一次，并在 "**评审 + 创建**" 页上下载预填充的模板以供将来重用。 

你还可以在创建群集期间启用[HDINSIGHT ID 代理](identity-broker.md)功能。 ID 代理功能允许使用多重身份验证登录到 Ambari，并获取所需的 Kerberos 票证，而不需要 Azure AD DS 中的密码哈希。

> [!NOTE]  
> ESP 群集名称的前六个字符在环境中必须是唯一的。 例如，如果在不同的虚拟网络中有多个 ESP 群集，请选择一种命名约定，以确保群集名称上的前六个字符是唯一的。

![Azure HDInsight 企业安全性套餐的域验证](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

启用 ESP 后，会自动检测并验证与 Azure AD DS 相关的常见错误配置。 修复这些错误后，你可以继续下一步。

![Azure HDInsight 企业安全性套餐域验证失败](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

使用 ESP 创建 HDInsight 群集时，必须提供以下参数：

- **群集管理员用户**：从同步的 Azure AD DS 实例中选择群集的管理员。 此域帐户必须已同步并且在 Azure AD DS 中可用。

- **群集访问组**：要同步其用户并有权访问群集的安全组应在 Azure AD DS 中可用。 例如，HiveUsers 组。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：示例 `ldaps://contoso.com:636`。

创建新群集时，可以从 "**用户分配的托管标识**" 下拉列表中选择你创建的托管标识。

![Azure HDInsight ESP Active Directory 域服务托管标识](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png)。

## <a name="next-steps"></a>后续步骤

* 有关如何配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Apache Hive 策略](apache-domain-joined-run-hive.md)。
* 有关如何使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Apache Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
