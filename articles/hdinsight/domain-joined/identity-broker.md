---
title: 使用 ID 代理（预览版）进行凭据管理 - Azure HDInsight
description: 了解 HDInsight ID 代理如何简化已加入域的 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 3b2807ccd6d83511dd0c9a32a177ea9fe2c4b642
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662100"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用 ID 代理（预览版）进行凭据管理

本文介绍了如何在 Azure HDInsight 中设置和使用 ID 代理功能。 你可以使用此功能通过 Azure 多重身份验证登录到 Apache Ambari，并获取所需的 Kerberos 票证，无需使用 Azure Active Directory 域服务 (Azure AD DS) 中的密码哈希。

## <a name="overview"></a>概述

在以下情况下，ID 代理简化了复杂的身份验证设置：

* 你的组织依赖于联合身份验证对访问云资源的用户进行身份验证。 以前，若要使用 HDInsight 企业安全性套餐 (ESP) 群集，你必须启用从本地环境到 Azure Active Directory 的密码哈希同步。 对于某些组织而言，此要求可能比较困难或不理想。

* 你正在构建的解决方案使用的技术依赖于不同的身份验证机制。 例如，Apache Hadoop 和 Apache Ranger 依赖于 Kerberos，而 Azure Data Lake Storage 依赖于 OAuth。

ID 代理提供统一的身份验证基础结构，不要求将密码哈希同步到 Azure AD DS。 ID 代理由 Windows Server VM（ID 代理节点）上运行的组件以及群集网关节点组成。 

下图显示了启用 ID 代理后针对所有用户（包括联合用户）的身份验证流：

![采用 ID 代理的身份验证流](./media/identity-broker/identity-broker-architecture.png)

使用 ID 代理，你可以通过多重身份验证登录到 ESP 群集，无需提供任何密码。 如果已登录到其他 Azure 服务（例如 Azure 门户），可以使用单一登录 (SSO) 体验登录到 HDInsight 群集。

## <a name="enable-hdinsight-id-broker"></a>启用 HDInsight ID 代理

若要创建启用了 ID 代理的 ESP 群集，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 执行 ESP 群集的基本创建步骤。 有关详细信息，请参阅[创建使用 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 选择“启用 HDInsight ID 代理”。

ID 代理功能将向群集添加一个额外的 VM。 此 VM 是 ID 代理节点，包括了用来支持身份验证的服务器组件。 ID 代理节点以域加入方式加入到 Azure AD DS 域。

![用于启用 ID 代理的选项](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
如果将名为 `idbrokernode` 且具有以下特性的一个新角色添加到模板的计算配置文件中，则会在启用 ID 代理节点的情况下创建集群：

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

HDInsight [IntelliJ 插件](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)已更新为支持 OAuth。 可以使用此插件连接到群集并提交作业。

还可以使用[适用于 VS Code 的 Spark & Hive Tools](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 来利用笔记本并提交作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>在 Azure AD DS 中在没有密码哈希的情况下进行 SSH 访问

启用 ID 代理后，对于使用域帐户的 SSH 方案，你仍然需要存储在 Azure AD DS 中的密码哈希。 若要通过 SSH 连接到已加入域的 VM，或者要运行 `kinit` 命令，你需要提供密码。 

SSH 身份验证要求 Azure AD DS 中存在哈希。 如果只想将 SSH 用于管理方案，则可创建一个纯云帐户，并使用该帐户通过 SSH 连接到群集。 其他用户仍可使用 Ambari 或 HDInsight 工具（例如 IntelliJ 插件），不需要在 Azure AD DS 中有密码哈希。

若要解决身份验证问题，请参阅此 [指南](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)。

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>客户端使用 OAuth 连接到设置了 ID 代理的 HDInsight 网关

在 ID 代理设置中，可以更新连接到网关的自定义应用和客户端，以便首先获取所需的 OAuth 令牌。 你可以按照此[文档](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)中的步骤使用以下信息获取令牌：

*   OAuth 资源 URI：`https://hib.azurehdinsight.net` 
* AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   权限：（名称：Cluster.ReadWrite，id：8f89faa0-ffef-4007-974d-4989b39ad77d）

获取 OAuth 令牌后，可以在 HTTP 请求的授权标头中使用该令牌， (例如 <clustername> -int.azurehdinsight.net) 。 例如，livy API 的示例卷命令可能如下所示：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)
