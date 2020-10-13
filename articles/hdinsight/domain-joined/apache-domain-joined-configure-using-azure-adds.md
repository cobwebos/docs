---
title: 带有 Azure AD DS 的企业安全性 - Azure HDInsight
description: 了解如何使用 Azure Active Directory 域服务设置和配置 HDInsight 企业安全性套餐群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 7792ac688ede32155ec32e1f4ba25b328102f86c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86079830"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>HDInsight 中带有 Azure Active Directory 域服务的企业安全性套餐配置

企业安全性套餐 (ESP) 群集在 Azure HDInsight 群集上提供多用户访问权限。 带有 ESP 的 HDInsight 群集连接到某个域。 此连接使域用户能够使用其域凭据向群集进行身份验证并运行大数据作业。

本文介绍了如何使用 Azure Active Directory 域服务 (Azure AD DS) 配置具有 ESP 的 HDInsight 群集。

> [!NOTE]  
> ESP 已在 HDInsight 3.6 和 4.0 中正式发布，适用于以下群集类型：Apache Spark、Interactive、Hadoop 和 HBase。 适用于 Apache Kafka 群集类型的 ESP 为预览版，我们尽最大努力提供支持。 在 ESP 正式发布日期（2018 年 10 月 1 日）之前创建的 ESP 群集不受支持。

## <a name="enable-azure-ad-ds"></a>启用 Azure AD DS

> [!NOTE]  
> 只有租户管理员有权启用 Azure AD DS。 如果群集存储是 Azure Data Lake Storage Gen1 或 Gen2，则必须只对需要使用基本 Kerberos 身份验证访问群集的用户禁用 Azure 多重身份验证。
>
> 可以使用[受信任 IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) 或[条件访问](../../active-directory/conditional-access/overview.md)仅在特定用户访问 HDInsight 群集的虚拟网络 IP 范围时对其禁用多重身份验证。 如果使用条件访问，请确保在 HDInsight 虚拟网络上启用了 Active Directory 服务终结点。
>
> 如果群集存储是 Azure Blob 存储，请不要禁用多重身份验证。

要想能够创建带有 ESP 的 HDInsight 群集，必须先启用 Azure AD DS。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。

默认情况下，启用 Azure AD DS 后，所有用户和对象将开始从 Azure Active Directory (Azure AD) 同步到 Azure AD DS。 同步操作的时长取决于 Azure AD 中对象的数目。 如果对象以十万记，则同步可能需要数天。

与 Azure AD DS 配合使用的域名必须不超过 39 个字符才能与 HDInsight 配合使用。

你可以选择仅同步需要访问 HDInsight 群集的组。 这种仅同步特定组的选项称为“范围有限的同步”。 有关说明，请参阅[配置从 Azure AD 到托管域的范围有限的同步](../../active-directory-domain-services/scoped-synchronization.md)。

启用安全 LDAP 时，请将域名置于使用者名称中， 并将使用者可选名称置于证书中。 如果域名为 contoso100.onmicrosoft.com，请确保证书使用者名称和使用者可选名称中存在完全匹配的名称。 有关详细信息，请参阅[为 Azure AD DS 托管域配置安全 LDAP](../../active-directory-domain-services/tutorial-configure-ldaps.md)。

以下示例创建一个自签名证书。 域名 contoso100.onmicrosoft.com 存在于 `Subject`（使用者名称）和 `DnsName`（使用者可选名称）中。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>检查 Azure AD DS 运行状况

在“管理”类别中选择“运行状况”，查看 Azure Active Directory 域服务的运行状况。  确保 Azure AD DS 的状态为绿色（正在运行），且同步已完成。

![Azure AD DS 运行状况](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>创建托管标识并为其授权

使用用户分配的托管标识来简化安全的域服务操作。 为托管标识分配 **HDInsight 域服务参与者**角色后，它就可以读取、创建、修改和删除域服务操作。

对于 HDInsight 企业安全性套餐，某些域服务操作是必需的，例如创建 OU 和服务主体。 可以在任何订阅中创建托管标识。 有关托管标识的常规详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 有关 Azure HDInsight 中托管标识的工作原理的详细信息，请参阅 [Azure HDInsight 中的托管标识](../hdinsight-managed-identities.md)。

若要设置 ESP 群集，请创建用户分配的托管标识（如果还没有）。 请参阅 [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

接下来，在 Azure AD DS **访问控制**中为托管标识分配“HDInsight 域服务参与者”角色。 你需要具有 Azure AD DS 管理员权限才能进行此角色分配。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

分配“HDInsight 域服务参与者”角色可确保此标识有适当的 (`on behalf of`) 访问权限，可以在 Azure AD DS 域上执行域服务操作。 这些操作包括创建和删除 OU。

为托管标识指定角色后，Azure AD DS 管理员可以对谁使用它进行管理。 首先，管理员在门户中选择该托管标识。 然后在“概览”下选择“访问控制(标识和访问管理)”。 管理员为需要创建 ESP 群集的用户或组分配“托管标识操作员”角色。

例如，Azure AD DS 管理员可以将此角色分配给“sjmsi”托管标识的“MarketingTeam”组。  下图中显示了一个示例。 此分配确保组织中的适当人员可以使用托管标识来创建 ESP 群集。

![HDInsight 托管标识操作者角色分配](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>网络注意事项

> [!NOTE]  
> Azure AD DS 必须部署在基于 Azure 资源管理器的虚拟网络中。 Azure AD DS 不支持经典虚拟网络。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)。

启用 Azure AD DS。 然后，本地域名系统 (DNS) 服务器会在 Active Directory 虚拟机 (VM) 上运行。 配置 Azure AD DS 虚拟网络来使用这些自定义 DNS 服务器。 若要找到正确的 IP 地址，请在“管理”类别中选择“属性”，然后在“虚拟网络上的 IP 地址”下查看。

![找到本地 DNS 服务器的 IP 地址](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

更改 Azure AD DS 虚拟网络中的 DNS 服务器的配置。 若要使用这些自定义 IP，请在“设置”类别中选择“DNS 服务器”。 然后选择“自定义”选项，在文本框中输入第一个 IP 地址，然后选择“保存”。  使用相同步骤添加更多的 IP 地址。

![更新虚拟网络 DNS 配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

将 Azure AD DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果打算使用不同的虚拟网络，必须将这些虚拟网络对等互连，以便 HDInsight VM 可以看见域控制器。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

将虚拟网络对等互连后，将 HDInsight 虚拟网络配置为使用自定义 DNS 服务器。 输入 Azure AD DS 专用 IP 作为 DNS 服务器地址。 当两个虚拟网络都使用相同的 DNS 服务器时，自定义域名将解析为正确的 IP 并可从 HDInsight 访问该域名。 例如，如果域名为 `contoso.com`，则在此步骤后，`ping contoso.com` 应能解析为正确的 Azure AD DS IP。

![为对等互连的虚拟网络配置自定义 DNS 服务器](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

如果在 HDInsight 子网中使用网络安全组 (NSG) 规则，应允许入站和出站流量[所需的 IP](../hdinsight-management-ip-addresses.md)。

若要测试网络连接设置，请将 Windows VM 加入 HDInsight 虚拟网络/子网并对域名执行 ping 命令。 （它应当能够解析为 IP。）运行 **ldp.exe** 来访问 Azure AD DS 域。 然后将此 Windows VM 加入域，以确认客户端和服务器之间所有必需的 RPC 调用均已成功。

使用 **nslookup** 确认是否可以通过网络访问你的存储帐户， 以及是否可以访问你可能使用的任何外部数据库（例如，外部 Hive 元存储或 Ranger DB）。 如果 NSG 保护 Azure AD DS，请确保在 Azure AD DS 子网的 NSG 规则中允许[必需的端口](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers)。 如果此 Windows VM 的域加入操作成功，则可继续执行下一步以创建 ESP 群集。

## <a name="create-an-hdinsight-cluster-with-esp"></a>创建带有 ESP 的 HDInsight 群集

正确设置前面的步骤后，下一步是创建启用了 ESP 的 HDInsight 群集。 创建 HDInsight 群集后，可以在“安全性 + 网络”选项卡上启用企业安全性套餐。对于用于部署的 Azure 资源管理器模板，请使用一次门户体验。 然后，从“审阅 + 创建”页面下载预填充的模板供将来重复使用。

也可在创建群集期间启用 [HDInsight ID 代理](identity-broker.md)功能。 可以使用 ID 代理功能通过多重身份验证登录到 Ambari，并获取所需的 Kerberos 票证，无需使用 Azure AD DS 中的密码哈希。

> [!NOTE]  
> ESP 群集名称的前六个字符在环境中必须是唯一的。 例如，如果你在不同虚拟网络中有多个 ESP 群集，请选择一个命名约定，该命名约定确保群集名称中的前六个字符是唯一的。

![Azure HDInsight 企业安全性套餐的域验证](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

启用 ESP 后，会自动检测与 Azure AD DS 相关的常见错误配置并对其进行验证。 纠正这些错误后，可以继续执行下一步。

![Azure HDInsight 企业安全性套餐域验证失败](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

创建带有 ESP 的 HDInsight 群集时，必须提供以下参数：

* **群集管理员用户**：从同步的 Azure AD DS 实例中为你的群集选择管理员。 此域帐户必须已同步并在 Azure AD DS 中可用。

* **群集访问组**：你要同步其用户且其用户有权访问群集的安全组应该在 Azure AD DS 中可用。 例如，HiveUsers 组。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

* **LDAPS URL**：例如 `ldaps://contoso.com:636`。

创建新群集时，可以从**用户分配的托管标识**下拉列表中选择已创建的托管标识。

![Azure HDInsight ESP Active Directory 域服务托管标识](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>后续步骤

* 有关如何配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Apache Hive 策略](apache-domain-joined-run-hive.md)。
* 有关如何使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Apache Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
