---
title: 使用 AAD-DS 配置已加入域的 HDInsight 群集
description: 了解如何使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 060ca8040f514ec1df48c2ca4568cbbb2a529267
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集

已加入域的群集在 HDInsight 上提供多用户企业安全功能。 已加入域的 HDInsight 群集连接到 Active Directory 域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 

本文介绍如何使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集。

> [!NOTE]
> 不再支持 Azure IaaS VM 上 的 Active Directory。

## <a name="create-azure-adds"></a>创建 Azure ADDS

在创建 HDInsight 群集之前，需要先创建 Azure AD DS。 若要创建 Azure ADDS，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租户管理员拥有创建域服务的特权。 如果将 Azure Data Lake 存储 (ADLS) 用作 HDInsight 的默认存储，则确保 ADLS 的默认 Azure AD 租户与 HDInsight 群集的域相同。 需对有权访问群集的用户禁用多重身份验证，才可将此安装程序用于 Azure Data Lake Store。

预配 AAD 域服务后，需要在 AAD 中创建一个具有合适权限的服务帐户（将同步到 AAD-DS），以用于创建 HDInsight 群集。 如果此服务帐户已存在，则需要重置其密码并等待，直到其同步到 AAD DS（此重置会导致创建 kerberos 密码哈希并且可能需要最多 30 分钟）。 此服务帐户应具有以下权限：

- 将计算机加入到域，并将计算机主体放到在创建群集期间指定的 OU 中。
- 在群集创建期间指定的 OU 内创建服务主体。

必须为 Azure AD 域服务托管域启用安全 LDAP。 要启用安全 LDAP，请参阅[为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>创建已加入域的 HDInsight 群集

下一步是使用前一部分中创建的 AAD DS 和服务帐户创建 HDInsight 群集。

将 Azure AD 域服务和 HDInsight 群集放在同一 Azure 虚拟网络 (VNet) 中会更方便。 如果它们在不同的 VNet 中，则必须在两个 VNet 之间建立对等互连。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

创建已加入域的 HDInsight 群集时，必须提供以下参数：

- **域名**：与 Azure AD DS 关联的域名。 例如 contoso.onmicrosoft.com
- **域用户名**：在前面的部分中在 Azure AD DC 中创建的服务帐户。 例如，hdiadmin@contoso.onmicrosoft.com。此域用户将成为此已加入域的 HDInsight 群集的管理员。
- **域密码**：服务帐户的密码。
- **组织单位**：要用于 HDInsight 群集的 OU 的可分辨名称。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com。如果此 OU 不存在，HDInsight 群集会尝试创建此 OU。 
- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636
- **访问用户组**：其用户要同步到群集的安全组。 例如，HiveUsers。 如果想要指定多个用户组，请使用逗号“,”分隔这些组。
 
> [!NOTE]
> 由于 Apache Zeppelin 使用域名对管理服务帐户进行身份验证，因此服务帐户必须具有与其 UPN 后缀相同的域名，Apache Zeppelin 才能正常工作。
 
以下屏幕截图显示了 Azure 门户中的配置：

![Azure HDInsight 中已加入域的 Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)。


## <a name="next-steps"></a>后续步骤
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

