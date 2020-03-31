---
title: 使用 ID 代理（预览）进行凭据管理 - Azure HDInsight
description: 了解 HDInsight ID 代理，以简化域加入的 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483006"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用 ID 代理（预览）进行凭据管理

本文介绍如何在 Azure HDInsight 中设置和使用 ID 代理功能。 您可以使用此功能通过 Azure 多重身份验证登录到 Apache Ambari，并获取所需的 Kerberos 票证，而无需在 Azure 活动目录域服务 （Azure AD DS） 中进行密码哈希。

## <a name="overview"></a>概述

ID 代理在以下情况下简化了复杂的身份验证设置：

* 您的组织依靠联合身份验证来验证用户访问云资源。 以前，要使用 HDInsight 企业安全包 （ESP） 群集，必须启用从本地环境到 Azure 活动目录的密码哈希同步。 对于某些组织来说，这一要求可能很困难或不可取。

* 您正在构建使用依赖于不同身份验证机制的技术的解决方案。 例如，Apache Hadoop 和 Apache 游侠依赖于 Kerberos，而 Azure 数据湖存储依赖于 OAuth。

ID 代理提供统一的身份验证基础结构，并消除了将密码哈希同步到 Azure AD DS 的要求。 ID 代理由在 Windows 服务器 VM（ID 代理节点）上运行的组件以及群集网关节点组成。 

下图显示了启用 ID Broker 后所有用户（包括联合用户）的身份验证流：

![使用 ID 代理的身份验证流](./media/identity-broker/identity-broker-architecture.png)

ID 代理允许您使用多重身份验证登录到 ESP 群集，而无需提供任何密码。 如果您已登录到其他 Azure 服务（如 Azure 门户），则可以使用单个登录 （SSO） 体验登录到 HDInsight 群集。

## <a name="enable-hdinsight-id-broker"></a>启用 HDInsight ID 代理

要创建启用 ID 代理的 ESP 群集，请执行以下步骤：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 按照 ESP 群集的基本创建步骤操作。 有关详细信息，请参阅使用[ESP 创建 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 选择**启用 HDInsight ID 代理**。

ID 代理功能将添加一个额外的 VM 到群集。 此 VM 是 ID 代理节点，包括支持身份验证的服务器组件。 ID 代理节点是加入到 Azure AD DS 域的域。

![启用 ID 代理的选项](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>工具集成

HDInsight [IntelliJ 插件](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)已更新以支持 OAuth。 您可以使用此插件连接到群集并提交作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中没有密码哈希的 SSH 访问

启用 ID 代理后，您仍然需要在 Azure AD DS 中存储的密码哈希，用于具有域帐户的 SSH 方案。 要 SSH 到加入域的 VM，或者要`kinit`运行该命令，您需要提供密码。 

SSH 身份验证要求哈希在 Azure AD DS 中可用。 如果只想将 SSH 用于管理方案，则可以创建一个仅云帐户，并将其用于群集的 SSH。 其他用户仍可以使用 Ambari 或 HDInsight 工具（如 IntelliJ 插件），而无需在 Azure AD DS 中提供密码哈希。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 活动目录域服务使用企业安全包配置 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)
