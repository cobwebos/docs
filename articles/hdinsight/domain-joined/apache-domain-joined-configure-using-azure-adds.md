---
title: 使用 Azure AD-DS 配置具有企业安全性套餐的 HDInsight 群集
description: 了解如何使用 Azure Active Directory 域服务设置和配置 HDInsight 企业安全性套餐群集
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968367"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集

企业安全性套餐 (ESP) 群集在 Azure HDInsight 群集上提供多用户访问权限。 具有 ESP 的 HDInsight 群集连接到域，使域用户能够使用其域凭据对群集进行身份验证和运行大数据作业。 

本文介绍如何使用 Azure Active Directory 域服务 (Azure AD-DS) 配置具有 ESP 的 HDInsight 群集。

>[!NOTE]
>ESP 适用于 Spark、Interactive 和 Hadoop 的 HDI 3.6+。 HBase 群集类型的 ESP 处于预览状态。


## <a name="enable-azure-ad-ds"></a>启用 Azure AD-DS

要想能够创建具有 ESP 的 HDInsight 群集，必须先启用 Azure AD-DS。 有关详细信息，请参阅[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 仅租户管理员有权创建 Azure AD-DS 实例。 如果将 Azure Data Lake Storage Gen1 用作 HDInsight 的默认存储，请确保 Data Lake Storage Gen1 的默认 Azure AD 租户与 HDInsight 群集的域相同。 由于 Hadoop 依赖于 Kerberos 和基本身份验证，因此需对有权访问群集的用户禁用多重身份验证。

安全 LDAP 适用于 Azure AD-DS 托管域。 启用 LDAPS 时，将域名置于使用者名称中或将使用者可选名称置于证书中。 有关详细信息，请参阅[为 Azure AD-DS 托管域配置安全 LDAP](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="add-managed-identity"></a>添加托管标识

启用 Azure AD-DS 后，创建托管标识并将其分配给 Azure AD-DS 访问控制中的 **HDInsight 域服务参与者**角色。

![Azure Active Directory 域服务访问控制](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

有关详细信息，请参阅[什么是 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="create-a-hdinsight-cluster-with-esp"></a>创建具有 ESP 的 HDInsight 群集

下一步是使用 Azure AD-DS 创建启用了 ESP 的 HDInsight 群集。

将 Azure AD-DS 实例和 HDInsight 群集放在同一 Azure 虚拟网络中会更方便。 如果选择将它们放在不同的虚拟网络中，则必须在那些虚拟网络之间建立对等互连，以便 HDInsight VM 能够与域控制器进行通信来使 VM 加入域。 有关详细信息，请参阅[虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)。

创建 HDInsight 群集后，可以启用企业安全性套餐以将群集与 Azure AD-DS 连接。 

![Azure HDInsight 安全和网络](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

启用 ESP 后，将自动检测与 Azure AD-DS 相关的常见错误配置并对其进行验证。

![Azure HDInsight 企业安全性套餐域验证](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

早期检测可以在创建群集之前修复错误，从而节省时间。

![Azure HDInsight 企业安全性套餐域验证失败](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

创建具有 ESP 的 HDInsight 群集时，必须提供以下参数：

- **群集管理员用户**：从同步的 Azure AD-DS 中选择群集的管理员。

- **群集访问组**：希望将其用户同步到群集的安全组应进行同步，并且可用于 Azure AD-DS。 例如，HiveUsers。 如果想要指定多个用户组，请使用分号“;”分隔。 预配之前，组必须存在于目录中。 有关详细信息，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 如果该组不存在，则会发生错误：“在 Active Directory 中找不到组 HiveUsers”。

- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636。

    > [!IMPORTANT]
    > 输入完整的 URL，包括“ldaps://”和端口号 (:636)。

以下屏幕截图显示了 Azure 门户中的配置：

   ![Azure HDInsight ESP Active Directory 域服务配置](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>后续步骤
* 有关配置 Hive 策略和运行 Hive 查询的信息，请参阅[为具有 ESP 的 HDInsight 群集配置 Hive 策略](apache-domain-joined-run-hive.md)。
* 有关使用 SSH 连接到具有 ESP 的 HDInsight 群集，请参阅[在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

