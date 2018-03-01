---
title: "使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集 - Azure | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集"
services: hdinsight
documentationcenter: 
author: bprakash
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: bhanupr
ms.openlocfilehash: 08795e6aafc6ccb43bad59189676a8680c03c966
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集

已加入域的群集在 HDInsight 上提供多用户企业安全功能。 已加入域的 HDInsight 群集连接到 Active Directory 域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 

可通过两种方法来设置已加入域的 HDInsight 群集可连接到的域控制器：

- Azure Active Directory 域服务 (Azure AD DS)
- Azure IaaS VM 上的 Active Directory 域控制器

本文介绍如何使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集。

## <a name="create-azure-adds"></a>创建 Azure ADDS

在创建 HDInsight 群集之前，需要先创建 Azure AD DS。 若要创建 Azure ADDS，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租户管理员拥有创建域服务的特权。 如果将 Azure Data Lake 存储 (ADLS) 用作 HDInsight 的默认存储，则确保 ADLS 的默认 Azure AD 租户与 HDInsight 群集的域相同。 需对有权访问群集的用户禁用多重身份验证，才可将此安装程序用于 Azure Data Lake Store。

预配域服务后，需要在“Azure AD DC 管理员”组中创建一个服务帐户用于创建 HDInsight 群集。 服务帐户必须是 Azure AD 上的全局管理员。

必须为 Azure AD 域服务托管域启用安全 LDAP。 要启用安全 LDAP，请参阅[为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>创建已加入域的 HDInsight 群集

下一步是使用前一部分中创建的 AAD DS 和服务帐户创建 HDInsight 群集。

将 Azure AD 域服务和 HDInsight 群集放在同一 Azure 虚拟网络 (VNet) 中会更方便。 如果它们在不同的 VNet 中，则必须在两个 VNet 之间建立对等互连。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

创建已加入域的 HDInsight 群集时，必须提供以下参数：

- **域名**：与 Azure AD DS 关联的域名。 例如 contoso.onmicrosoft.com
- **域用户名**：“Azure AD DC 管理员组”中的服务帐户，已在上一部分创建。 例如，hdiadmin@contoso.onmicrosoft.com。此域用户是此已加入域的 HDInsight 群集的管理员。
- **域密码**：服务帐户的密码。
- **组织单位**：要用于 HDInsight 群集的 OU 的可分辨名称。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com。如果此 OU 不存在，HDInsight 群集会尝试创建此 OU。 
- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636
- **访问用户组**：其用户要同步到群集的安全组。 例如，HiveUsers。 如果想要指定多个用户组，请使用逗号“,”分隔这些组。
 
以下屏幕截图显示了 Azure 门户中的配置：

![Azure HDInsight 中已加入域的 Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png)。


## <a name="next-steps"></a>后续步骤
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关使用 SSH 连接到已加入域的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

