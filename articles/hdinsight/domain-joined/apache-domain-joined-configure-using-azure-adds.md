---
title: 使用 AAD-DS 配置已加入域的 HDInsight 群集
description: 了解如何使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849816"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集

已加入域的群集在 HDInsight 群集上提供多用户访问。 已加入域的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 

本文介绍如何使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集。

## <a name="create-azure-adds"></a>创建 Azure ADDS

要想能够创建已加入域的 HDInsight 群集，启用 Azure AD 域服务 (AAD-DS) 是一个先决条件。 若要启用 AAD-DS 实例，请参阅[如何使用 Azure 门户启用 AAD-DS](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租户管理员有权创建 AAD-DS 实例。 如果将 Azure Data Lake 存储 (ADLS) 用作 HDInsight 的默认存储，则确保 ADLS 的默认 Azure AD 租户与 HDInsight 群集的域相同。 由于 Hadoop 依赖于 Kerberos 和基本身份验证，因此，需要对有权访问群集的用户禁用多重身份验证，才可将此安装程序用于 Azure Data Lake Store。

预配 AAD-DS 实例后，需要在 AAD 中创建一个具有合适权限的服务帐户（将同步到 AAD-DS）。 如果此服务帐户已存在，则需要重置其密码并等待，直到其同步到 AAD DS（此重置会导致创建 kerberos 密码哈希并且可能需要多达 30 分钟来同步到 AAD-DS）。 此服务帐户应具有以下权限：

- 将计算机加入到域，并将计算机主体放到在创建群集期间指定的 OU 中。
- 在群集创建期间指定的 OU 内创建服务主体。

> [!NOTE]
> 由于 Apache Zeppelin 使用域名对管理服务帐户进行身份验证，因此服务帐户必须具有与其 UPN 后缀相同的域名，Apache Zeppelin 才能正常工作。

若要详细了解 OU 以及如何管理它们，请参阅[在 AAD-DS 上创建 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。 

需要 AAD DS 托管域的安全 LDAP。 若要启用安全 LDAP，请参阅[如何为 AAD-DS 托管域配置安全 LDAP (LDAPS)](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>创建已加入域的 HDInsight 群集

下一步是使用前一部分中创建的 AAD-DS 和服务帐户创建 HDInsight 群集。

将 AAD-DS 和 HDInsight 群集放在同一 Azure 虚拟网络 (VNet) 中更容易。 如果选择将它们放在不同的 VNet 中，则必须在那些 VNet 之间建立对等互连，以便 HDI VM 能够与域控制器进行通信来使 VM 加入域。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

创建已加入域的 HDInsight 群集时，必须提供以下参数：

- **域名**：与 AAD-DS 关联的域名。 例如 contoso.onmicrosoft.com
- **域用户名**：在前面的部分中在托管域中创建的服务帐户。 例如，hdiadmin@contoso.onmicrosoft.com。 此域用户将成为此 HDInsight 群集的管理员。
- **域密码**：服务帐户的密码。
- **组织单位**：要用于 HDInsight 群集的 OU 的可分辨名称。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com。 如果此 OU 不存在，则 HDInsight 群集会尝试使用服务帐户拥有的权限创建 OU。 （例如，如果服务帐户在 AAD-DS 管理员组中，则它有合适的权限来创建 OU，否则，你可能需要首先创建 OU 并首先向服务帐户授予对该 OU 的完全控制权限 - 请参阅[在 AAD-DS 上创建 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)）。

    > [!IMPORTANT]
    > 请务必在 OU 后包括所有 DC 并以逗号分隔它们（例如 OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com）。

- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > 请输入完整的 URL，包括 ldaps:// 和端口号 (:636)

- **访问用户组**：其用户要同步到群集的安全组。 例如，HiveUsers。 如果想要指定多个用户组，请使用逗号“,”分隔这些组。
 
以下屏幕截图显示了 Azure 门户中的配置：

![Azure HDInsight 中已加入域的 Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)。


## <a name="next-steps"></a>后续步骤
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

