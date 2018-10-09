---
title: 使用 Azure AD-DS 配置具有企业安全性套餐的 HDInsight 群集
description: 了解如何使用 Azure Active Directory 域服务设置和配置 HDInsight 企业安全性套餐群集。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408348"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集

企业安全性套餐 (ESP) 群集在 Azure HDInsight 群集上提供多用户访问权限。 具有 ESP 的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 

本文介绍如何使用 Azure Active Directory 域服务 (Azure AD-DS) 配置具有 ESP 的 HDInsight 群集。

>[!NOTE]
>ESP 在适用于 Spark、Interactive 和 Hadoop 的 HDI 3.6 中为公开发行版。 HBase 和 Kafka 群集类型的 ESP 为预览版。

## <a name="enable-azure-ad-ds"></a>启用 Azure AD-DS

要想能够创建具有 ESP 的 HDInsight 群集，必须先启用 Azure AD-DS。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

默认情况下，启用 Azure AD-DS 后，所有用户和对象就开始从 Azure Active Directory (AAD) 同步到 Azure AD-DS。 同步操作的时长取决于 AAD 中对象的数目。 如果对象数以十万记，则同步可能需要数天。 

客户可以选择仅同步需要访问 HDInsight 群集的组。 这种仅同步特定组的选项称为“范围有限的同步”。 有关说明，请参阅 [Configure Scoped Synchronization from Azure AD to your managed domain](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization)（配置从 Azure AD 到托管域的范围有限的同步）。

> [!NOTE]
> 仅租户管理员有权创建 Azure AD-DS 实例。 只需对有权访问群集的用户禁用多重身份验证。

启用安全 LDAP 时，请将域名置于使用者名称中或将使用者可选名称置于证书中。 例如，如果域名为 *contoso.com*，请确保证书所有者名称或使用者可选名称中存在完全匹配的名称。 有关详细信息，请参阅[为 Azure AD-DS 托管域配置安全 LDAP](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="check-aad-ds-health-status"></a>查看 AAD-DS 运行状况

在“管理”类别下选择“运行状况”，查看 Azure Active Directory 域服务的运行状况。 确保 AAD-DS 的状态为绿色（正在运行），且同步已完成。

![Azure Active Directory 域服务运行状况](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>添加托管标识

启用 Azure AD-DS 后，创建用户分配的托管标识并将其分配给 Azure AD-DS 访问控制中的 **HDInsight 域服务参与者**角色。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

将托管标识分配给“HDInsight 域服务参与者”角色可确保标识有适当的访问权限，可以在 AAD-DS 域上执行某些域服务操作。 有关详细信息，请参阅[什么是 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="create-a-hdinsight-cluster-with-esp"></a>创建具有 ESP 的 HDInsight 群集

下一步是使用 Azure AD-DS 创建启用了 ESP 的 HDInsight 群集。

将 Azure AD-DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果选择将它们放在不同的虚拟网络中，则必须在那些虚拟网络之间建立对等互连，以便 HDInsight VM 能够与域控制器进行通信来使 VM 加入域。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。 若要测试对等互连是否已正确完成，请将 VM 加入 HDInsight VNET/子网，然后 ping 域名，或者运行 **ldp.exe** 来访问 AAD-DS 域。

创建 HDInsight 群集后，可以在自定义选项卡中启用企业安全性套餐。 

![Azure HDInsight 安全和网络](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

启用 ESP 后，将自动检测与 Azure AD-DS 相关的常见错误配置并对其进行验证。

![Azure HDInsight 企业安全性套餐域验证](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

早期检测可以在创建群集之前修复错误，从而节省时间。

![Azure HDInsight 企业安全性套餐域验证失败](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

创建具有 ESP 的 HDInsight 群集时，必须提供以下参数：

- **群集管理员用户**：从同步的 Azure AD-DS 中选择群集的管理员。 此帐户必须已同步并在 AAD-DS 中可用。

- **群集访问组**：希望将其用户同步到群集的安全组应该在 Azure AD-DS 中可用。 例如，HiveUsers 组。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

- **LDAPS URL**：例如 ldaps://contoso.com:636。

以下屏幕截图显示了 Azure 门户中的成功配置：

![Azure HDInsight ESP Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

创建新群集时，已创建的托管标识可以从用户分配的托管标识下拉列表中选择。

![Azure HDInsight ESP Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>后续步骤
* 有关配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Hive 策略](apache-domain-joined-run-hive.md)。
* 有关使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。