---
title: 使用 ID 代理（预览版）进行凭据管理 - Azure HDInsight
description: 了解 HDInsight ID 代理如何简化已加入域的 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 8f1e0a6aecc9702552a3dd66acc8dc7eb5bf1d85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529913"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID 代理 (预览) 

本文介绍如何在 Azure HDInsight 中设置和使用 HDInsight ID Broker (HIB) 功能。 你可以使用此功能获取对 Apache Ambari 的新式 OAuth 身份验证，同时 (MFA) 强制执行，而无需在 Azure Active Directory 域服务 (AAD-DS) 中使用旧密码哈希。

## <a name="overview"></a>概述

在以下情况下，HIB 简化了复杂的身份验证设置：

* 你的组织依赖于联合身份验证对访问云资源的用户进行身份验证。 以前，若要使用 HDInsight 企业安全性套餐 (ESP) 群集，你必须从本地环境启用密码哈希同步，以 Azure Active Directory (Azure AD) 。 对于某些组织而言，此要求可能比较困难或不理想。

* 你的组织想要对 Apache Ambari 和其他群集资源强制实施基于 web/HTTP 的 MFA。

HIB 提供身份验证基础结构，该基础结构允许从 OAuth (新式) 到 Kerberos (旧的) ，无需将密码哈希同步到 AAD DS。 此基础结构包含在 Windows Server VM 上运行的组件 (ID 代理节点) 以及群集网关节点。

下图显示了启用 ID 代理后所有用户（包括联合用户）的新式基于 OAuth 的身份验证流：

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="采用 ID 代理的身份验证流":::

在此图中，客户端 (（即浏览器或应用) 需要首先获取 OAuth 令牌，然后向 HTTP 请求中的网关提供令牌。 如果已登录到其他 Azure 服务（例如 Azure 门户），可以使用单一登录 (SSO) 体验登录到 HDInsight 群集。

仍有许多仅支持基本身份验证的旧版应用程序 (即用户名/密码) 。 对于这些方案，你仍可以使用 HTTP 基本身份验证连接到群集网关。 在此设置中，你必须确保从网关节点到联合终结点的网络连接 (ADFS 终结点) ，以确保可从网关节点直接看到行。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="采用 ID 代理的身份验证流":::

根据组织的需要，使用下表确定最佳身份验证选项：

|身份验证选项 |HDInsight 配置 | 要考虑的因素 |
|---|---|---|
| 完全 OAuth | ESP + HIB | 1. 支持) 2 (MFA 的最安全选项。    不需要传递哈希同步。 3.  无 kinit/keytab 访问权限，无法访问 AAD-DS 中没有密码哈希的本地帐户。 4.   仅限云的帐户可为 ssh/kinit/keytab。 5. 通过 Oauth 6 对 Ambari 进行基于 Web 的访问。  需要 (JDBC/ODBC 等 ) 更新旧版应用程序以支持 OAuth。|
| OAuth + 基本身份验证 | ESP + HIB | 1. 通过 Oauth 2 对 Ambari 的基于 Web 的访问。 旧应用继续使用基本身份验证3。 必须禁用 MFA 才能进行基本身份验证访问。 4. 不需要传递哈希同步。 5. 无 kinit/keytab 访问权限，无法访问 AAD-DS 中没有密码哈希的本地帐户。 6. 仅限云的帐户仍可以 ssh/kinit。 |
| 完全基本身份验证 | ESP | 1. 最类似于本地的设置。 2. 需要将密码哈希同步到 AAD-DS。 3. 本地帐户可以使用 ssh/kinit 或使用 keytab。 4. 如果支持存储 ADLS Gen2，则必须禁用 MFA |


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

HDIsngith 工具已更新为本机支持 OAuth。 我们强烈建议将这些工具用于基于的新式 OAuth 访问群集。 HDInsight [IntelliJ 插件](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 可用于基于 JAVA 的应用程序，如 Scala。 [Spark & 用于 VS Code 的 Hive 工具](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 可用于 PySpark 和 Hive 作业。 它们支持批处理和交互式作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>在 Azure AD DS 中在没有密码哈希的情况下进行 SSH 访问

|SSH 选项 |要考虑的因素 |
|---|---|
| 本地 VM 帐户 (例如 sshuser)  | 1. 在创建群集时提供此帐户。 2.  此帐户没有 kerberos authication |
| 仅限云帐户 (例如 alice@contoso.onmicrosoft.com)  | 1. AAD-DS 2 中提供了密码哈希。 可以通过 SSH kerberos 进行 kerberos 身份验证 |
| 本地帐户 (例如 alice@contoso.com)  | 1. 只有在 AAD-DS 中提供密码哈希时，才可以使用 SSH Kerberos 身份验证，否则该用户无法通过 SSH 连接到群集 |

若要通过 SSH 连接到已加入域的 VM，或者要运行 `kinit` 命令，你需要提供密码。 SSH Kerberos 身份验证需要哈希在 AAD-DS 中可用。 如果只想将 SSH 用于管理方案，则可创建一个纯云帐户，并使用该帐户通过 SSH 连接到群集。 其他本地用户仍可使用 Ambari 或 HDInsight 工具或 HTTP 基本身份验证，而无需在 AAD-DS 中提供密码哈希。

如果你的组织不将密码哈希同步到 AAD DS，最佳做法是在 Azure AD 中创建一个仅限云的用户，并在创建群集时将其分配为群集管理员，并使用该用户进行管理，其中包括通过 SSH 获取 Vm 的根访问权限。

若要排查身份验证问题，请参阅此[指南](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)。

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>使用 OAuth 通过 HIB 连接到 HDInsight 网关的客户端

在 HIB 设置中，可以更新连接到网关的自定义应用和客户端，以便首先获取所需的 OAuth 令牌。 你可以按照此[文档](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)中的步骤使用以下信息获取令牌：

*   OAuth 资源 URI：`https://hib.azurehdinsight.net` 
*   AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   权限：（名称：Cluster.ReadWrite，id：8f89faa0-ffef-4007-974d-4989b39ad77d）

获取 OAuth 令牌后，可以在 HTTP 请求的授权标头中使用该令牌， (例如 https:// <clustername> -int.azurehdinsight.net) 。 例如，Apache Livy API 的示例卷曲命令可能如下所示：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)
