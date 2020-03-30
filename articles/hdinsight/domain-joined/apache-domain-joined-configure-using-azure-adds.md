---
title: 使用 Azure AD DS 的企业安全性 - Azure HDInsight
description: 了解如何使用 Azure 活动目录域服务设置和配置 HDInsight 企业安全包群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272833"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>HDInsight 中具有 Azure 活动目录域服务的企业安全包配置

企业安全包 （ESP） 群集在 Azure HDInsight 群集上提供多用户访问。 具有 ESP 的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。

在本文中，您将了解如何使用 Azure 活动目录域服务 （Azure AD DS） 使用 ESP 配置 HDInsight 群集。

> [!NOTE]  
> ESP 通常可用于 HDInsight 3.6 和 4.0 中，适用于这些群集类型：Apache Spark、交互式、Hadoop 和 HBase。 Apache Kafka 群集类型的 ESP 处于预览状态，仅提供尽力支持。 不支持在 ESP GA 日期（2018 年 10 月 1 日之前创建的 ESP 群集）。

## <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

> [!NOTE]  
> 只有租户管理员才具有启用 Azure AD DS 的权限。 如果群集存储是 Azure 数据存储第 1 代或第 2 代，则仅对需要使用基本 Kerberos 身份验证访问群集的用户禁用 Azure 多重身份验证。 
>
> *仅当*特定用户访问 HDInsight 群集虚拟网络的 IP 范围时，才能使用[受信任的 IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips)或[条件访问](../../active-directory/conditional-access/overview.md)来禁用多因素身份验证。 如果使用条件访问，请确保 HDInsight 虚拟网络上的 Active Directory 服务终结点已启用。
>
> 如果群集存储是 Azure Blob 存储，则不要禁用多重身份验证。

启用 Azure AD DS 是使用 ESP 创建 HDInsight 群集的先决条件。 有关详细信息，请参阅使用[Azure 门户启用 Azure 活动目录域服务](../../active-directory-domain-services/tutorial-create-instance.md)。 

启用 Azure AD DS 后，默认情况下，所有用户和对象都开始从 Azure 活动目录 （Azure AD） 同步到 Azure AD DS。 同步操作的时长取决于 Azure AD 中对象的数目。 对于数十万个对象，同步可能需要几天时间。 

与 Azure AD DS 一起使用的域名必须为 39 个字符或更少，才能使用 HDInsight。

您可以选择仅同步需要访问 HDInsight 群集的组。 这种仅同步特定组的选项称为“范围有限的同步”。** 有关说明，请参阅[将范围化同步从 Azure AD 配置到托管域](../../active-directory-domain-services/scoped-synchronization.md)。

启用安全 LDAP 时，将域名放在主题名称和证书中的主题替代名称中。 例如，如果域名为 *contoso100.onmicrosoft.com*，请确保证书所有者名称和使用者可选名称中存在完全匹配的名称。 有关详细信息，请参阅[为 Azure AD DS 托管域配置安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。 

下面的示例创建一个自签名证书。 域名*contoso100.onmicrosoft.com*在（主题名称）`Subject`和`DnsName`（主题替代名称）中。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>检查 Azure AD DS 运行状况状态
通过在 **"管理"** 类别中选择**运行状况**，查看 Azure 活动目录域服务的运行状况。 确保 Azure AD DS 的状态为绿色（正在运行），并且同步已完成。

![Azure AD DS 运行状况](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>创建和授权托管标识

您可以使用*用户分配的托管标识*来简化和帮助保护域服务操作。 将**HDInsight 域服务参与者**角色分配给托管标识时，它可以读取、创建、修改和删除域服务操作。 

HDInsight 企业安全包需要某些域服务操作，如创建 OEM 和服务主体。 您可以在任何订阅中创建托管标识。 有关托管标识的详细信息，请参阅[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 有关托管标识在 Azure HDInsight 中的工作方式的详细信息，请参阅[Azure HDInsight 中的托管标识](../hdinsight-managed-identities.md)。

要设置 ESP 群集，请创建用户分配的托管标识（如果尚未创建）。 有关说明，请参阅[使用 Azure 门户创建、列出、删除或将角色分配给用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

接下来，将**HDInsight 域服务参与者**角色分配给 Azure AD DS**访问控件**中的托管标识。 您需要 Azure AD DS 管理员权限才能分配此角色。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

分配**HDInsight 域服务参与者**角色可确保此标识具有对 Azure AD DS 域执行域服务操作的适当（代表）访问权限。 这些操作包括创建和删除 O。

创建托管标识并给出正确的角色后，Azure AD DS 管理员可以设置谁可以使用此托管标识。 首先，管理员在门户中选择托管标识，然后在 **"概述**"下选择**访问控制 （IAM）。** 然后，在右侧，管理员将**托管标识操作员**角色分配给希望创建 HDInsight ESP 群集的用户或组。 

例如，Azure AD DS 管理员可以将此角色分配给**sjmsi**托管标识**的市场营销团队**组，如下图所示。 此分配可确保组织中的正确人员可以使用托管标识创建 ESP 群集。

![HDInsight 托管标识操作者角色分配](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>网络注意事项

> [!NOTE]  
> Azure AD DS 必须部署在基于 Azure 资源管理器的虚拟网络中。 Azure AD DS 不支持经典虚拟网络。 有关详细信息，请参阅使用[Azure 门户启用 Azure 活动目录域服务](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

启用 Azure AD DS 后，本地域名系统 （DNS） 服务器在活动目录虚拟机 （VM） 上运行。 将 Azure AD DS 虚拟网络配置为使用这些自定义 DNS 服务器。 要查找正确的 IP 地址，请选择 **"管理**类别中**的属性**"，然后查看**VIRTUAL 网络 上的 IP 地址**。

![查找本地 DNS 服务器的 IP 地址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

通过在"**设置"** 类别中选择**DNS 服务器**，更改 Azure AD DS 虚拟网络中 DNS 服务器的配置以使用这些自定义 IP。 然后选择 **"自定义"** 选项，在文本框中输入第一个 IP 地址，然后选择 **"保存**"。 使用相同的步骤添加更多 IP 地址。

![更新虚拟网络 DNS 配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

将 Azure AD DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果计划使用不同的虚拟网络，则必须对这些虚拟网络进行对等，以便域控制器对 HDInsight VM 可见。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。 

对等虚拟网络后，将 HDInsight 虚拟网络配置为使用自定义 DNS 服务器，并将 Azure AD DS 专用 IP 作为 DNS 服务器地址输入。 当两个虚拟网络使用相同的 DNS 服务器时，您的自定义域名将解析为正确的 IP，并且可以从 HDInsight 访问。 例如，如果您的域名是`contoso.com`，则在此步骤之后，`ping contoso.com`应解析为正确的 Azure AD DS IP。

![为对等虚拟网络配置自定义 DNS 服务器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果您在 HDInsight 子网中使用网络安全组 （NSG） 规则，则应允许对入站和出站流量[所需的 IP。](../hdinsight-management-ip-addresses.md)

要测试网络设置是否正确，请将 Windows VM 加入 HDInsight 虚拟网络/子网并 ping 域名。 （它应该解析为 IP。运行**ldp.exe**以访问 Azure AD DS 域。 然后，将此 Windows VM 加入域，以确认客户端和服务器之间所需的所有 RPC 调用是否成功。 

您还可以使用**nslookup**确认对存储帐户或任何可能使用的外部数据库（例如，外部 Hive 元存储或 Ranger DB）的网络访问。 如果 NSG 有助于保护 Azure AD DS，请确保 Azure AD DS 子网的 NSG 规则中允许所有[必需的端口](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)。 如果此 Windows VM 的域加入成功，则可以继续执行下一步并创建 ESP 群集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>使用 ESP 创建 HDInsight 群集

正确设置上述步骤后，下一步是创建启用 ESP 的 HDInsight 群集。 创建 HDInsight 群集时，您可以在 **"安全 + 网络"** 选项卡上启用企业安全包。如果希望使用 Azure 资源管理器模板进行部署，请使用门户体验一次，并在 **"审核 + 创建**"页上下载预填充模板以供将来重用。 

您还可以在群集创建期间启用[HDInsight ID 代理](identity-broker.md)功能。 ID 代理功能允许您使用多重身份验证登录到 Ambari，并获取所需的 Kerberos 票证，而无需在 Azure AD DS 中进行密码哈希。

> [!NOTE]  
> ESP 群集名称的前六个字符在环境中必须是唯一的。 例如，如果在不同的虚拟网络中有多个 ESP 群集，请选择一个命名约定，以确保群集名称上的前六个字符是唯一的。

![Azure HDInsight 企业安全包的域验证](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

启用 ESP 后，将自动检测和验证与 Azure AD DS 相关的常见错误配置。 修复这些错误后，可以继续执行下一步。

![Azure HDInsight 企业安全包失败域验证](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

使用 ESP 创建 HDInsight 群集时，必须提供以下参数：

- **群集管理员用户**：从同步的 Azure AD DS 实例中选择群集的管理员。 此域帐户必须已同步并在 Azure AD DS 中可用。

- **群集访问组**：要同步并有权访问群集的用户的安全组应在 Azure AD DS 中可用。 例如，HiveUsers 组。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：`ldaps://contoso.com:636`一个示例是 。

创建新群集时，可以从**用户分配的托管标识**下拉列表选择您创建的托管标识。

![Azure HDInsight ESP 活动目录域服务托管标识](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>后续步骤

* 有关如何配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Apache Hive 策略](apache-domain-joined-run-hive.md)。
* 有关如何使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Apache Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
