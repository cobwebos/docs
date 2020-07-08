---
title: 使用 ID Broker （预览版）进行凭据管理-Azure HDInsight
description: 了解 HDInsight ID 代理以简化已加入域的 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: ff7cb3c03edf9b421347815311796896caaffd70
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086596"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用 ID Broker （预览版）进行凭据管理

本文介绍如何在 Azure HDInsight 中设置和使用 ID 代理功能。 可以使用此功能通过 Azure 多重身份验证登录到 Apache Ambari，并获取所需的 Kerberos 票证，而不需要 Azure Active Directory 域服务（Azure AD DS）中的密码哈希。

## <a name="overview"></a>概述

在以下情况下，ID Broker 简化了复杂的身份验证设置：

* 你的组织依赖于联合身份验证访问云资源的用户。 以前，若要使用 HDInsight 企业安全性套餐（ESP）群集，你必须启用从本地环境到 Azure Active Directory 的密码哈希同步。 对于某些组织而言，此要求可能比较困难或不理想。

* 你正在构建的解决方案使用的技术依赖于不同的身份验证机制。 例如，Apache Hadoop 和 Apache Ranger 依赖于 Kerberos，而 Azure Data Lake Storage 依赖于 OAuth。

ID 代理提供统一的身份验证基础结构，消除了将密码哈希同步到 Azure AD DS 的要求。 ID Broker 包含 Windows Server VM （ID 代理节点）上运行的组件以及群集网关节点。 

下图显示了启用 ID 代理后所有用户（包括联合用户）的身份验证流：

![ID 为 Broker 的身份验证流](./media/identity-broker/identity-broker-architecture.png)

ID 代理允许使用多重身份验证登录到 ESP 群集，而无需提供任何密码。 如果已登录到其他 Azure 服务（如 Azure 门户），则可以使用单一登录（SSO）体验登录到 HDInsight 群集。

## <a name="enable-hdinsight-id-broker"></a>启用 HDInsight ID 代理

若要创建启用了 ID Broker 的 ESP 群集，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 遵循 ESP 群集的基本创建步骤。 有关详细信息，请参阅[创建具有 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 选择 "**启用 HDINSIGHT ID 代理**"。

ID 代理功能将向群集添加一个额外的 VM。 此 VM 是 ID 代理节点，包括支持身份验证的服务器组件。 ID 代理节点是加入到 Azure AD DS 域的域。

![用于启用 ID 代理的选项](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
如果将具有以下属性的名为的新角色添加 `idbrokernode` 到模板的计算配置文件中，则会在启用 ID 代理节点的情况下创建群集：

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>工具集成

HDInsight [IntelliJ 插件](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)将更新为支持 OAuth。 可以使用此插件连接到群集并提交作业。

你还可以使用[Spark & Hive 工具来 VS Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode)来利用笔记本和提交作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中没有密码哈希的 SSH 访问

启用 ID 代理后，仍需要使用域帐户为 SSH 方案 Azure AD DS 存储密码哈希。 若要通过 SSH 连接到已加入域的 VM，或者若要运行该 `kinit` 命令，则需要提供密码。 

SSH 身份验证要求 Azure AD DS 中提供哈希。 如果只想将 SSH 用于管理方案，则可以创建一个仅限云的帐户，并使用该帐户通过 SSH 连接到群集。 其他用户仍可使用 Ambari 或 HDInsight 工具（例如 IntelliJ 插件），Azure AD DS 中没有可用的密码哈希。

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>使用 OAuth 连接到具有 ID 代理安装程序的 HDInsight 网关的客户端

在 ID 代理安装中，可以更新连接到网关的自定义应用和客户端，以便首先获取所需的 OAuth 令牌。 您可以按照本[文档](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)中的步骤获取令牌，其中包含以下信息：

*   OAuth 资源 uri：`https://hib.azurehdinsight.net` 
* AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   权限：（name： cluster.exe，id：8f89faa0-ffef-4007-974d-4989b39ad77d）

## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory 域服务企业安全性套餐配置 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)
