---
title: 使用 ID 代理 (预览) 进行凭据管理-Azure HDInsight
description: 了解 Azure HDInsight ID 代理，以简化加入域 Apache Hadoop 群集的身份验证。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 6d4539e5dbc7182386a60317a9ee45a986ffd61f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999951"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID 代理 (预览) 

本文介绍如何设置和使用 Azure HDInsight ID 代理功能。 你可以使用此功能在执行多重身份验证时，获取对 Apache Ambari 的新式 OAuth 身份验证，而无需 Azure Active Directory 域服务 (Azure AD DS) 中的旧密码哈希。

## <a name="overview"></a>概述

在以下情况下，HDInsight ID 代理简化了复杂的身份验证设置：

* 你的组织依赖于联合身份验证对访问云资源的用户进行身份验证。 以前，若要使用 HDInsight 企业安全性套餐群集，你必须从本地环境启用密码哈希同步，以 Azure Active Directory (Azure AD) 。 对于某些组织而言，此要求可能比较困难或不理想。
* 你的组织希望为 Apache Ambari 和其他群集资源的基于 web 或基于 HTTP 的访问强制执行多重身份验证。

HDInsight ID 代理提供身份验证基础结构，该基础结构允许从 OAuth (新式) 到 Kerberos (旧的) ，无需将密码哈希同步到 Azure AD DS。 此基础结构包含在 Windows Server 虚拟机上运行的组件， (启用了 HDInsight ID 代理节点的 Windows Server 虚拟机) 以及群集网关节点。

根据组织的需求，使用下表确定最佳身份验证选项。

|身份验证选项 |HDInsight 配置 | 要考虑的因素 |
|---|---|---|
| 完全 OAuth | 企业安全性套餐 + HDInsight ID 代理 | 最安全的选项。  (多因素身份验证受支持。 *不* 需要 ) 传递哈希同步。 没有 Azure AD DS 中没有密码哈希的本地帐户的 ssh/kinit/keytab 访问权限。 仅限云的帐户可为 ssh/kinit/keytab。 通过 OAuth 对 Ambari 进行基于 Web 的访问。 需要更新旧版应用 (例如，JDBC/ODBC) 支持 OAuth。|
| OAuth + 基本身份验证 | 企业安全性套餐 + HDInsight ID 代理 | 通过 OAuth 对 Ambari 进行基于 Web 的访问。 旧应用继续使用基本身份验证。对于基本身份验证访问，必须禁用多重身份验证。 *不*需要传递哈希同步。 没有 Azure AD DS 中没有密码哈希的本地帐户的 ssh/kinit/keytab 访问权限。 仅限云的帐户仍可以 ssh/kinit。 |
| 完全基本身份验证 | 企业安全性套餐 | 最类似于本地设置。 需要将密码哈希同步到 Azure AD DS。 本地帐户可以使用 ssh/kinit，也可以使用 keytab。 如果 Azure Data Lake Storage Gen2 备份存储，则必须禁用多重身份验证。 |

下图显示了在启用 HDInsight ID 代理后，所有用户（包括联合用户）的新式基于 OAuth 的身份验证流：

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="显示 HDInsight ID 代理的身份验证流的关系图。":::

在此关系图中，客户端 (也就是说，浏览器或应用) 需要首先获取 OAuth 令牌。 然后，它将令牌提供给 HTTP 请求中的网关。 如果你已登录到其他 Azure 服务（例如 Azure 门户），则可以使用单一登录体验登录到 HDInsight 群集。

还可能存在许多仅支持基本身份验证的旧版应用程序 (即，用户名和密码) 。 对于这些方案，你仍可以使用 HTTP 基本身份验证连接到群集网关。 在此设置中，你必须确保从网关节点到 Active Directory 联合身份验证服务 (AD FS) 终结点的网络连接，以确保可从网关节点直接看到的行。

下图显示了联合用户的基本身份验证流程。 首先，网关尝试使用 [ROPC flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)完成身份验证。 如果没有同步到 Azure AD 的密码哈希，则会回退到发现 AD FS 终结点并通过访问 AD FS 终结点完成身份验证。

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="显示 HDInsight ID 代理的身份验证流的关系图。":::


## <a name="enable-hdinsight-id-broker"></a>启用 HDInsight ID 代理

若要创建启用了 HDInsight ID Broker 的企业安全性套餐群集：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 按照企业安全性套餐群集的基本创建步骤操作。 有关详细信息，请参阅 [创建具有企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)。
1. 选择“启用 HDInsight ID 代理”。

HDInsight ID 代理功能向群集中添加了一个额外的 VM。 此 VM 是 HDInsight ID 代理节点，它包括支持身份验证的服务器组件。 HDInsight ID 代理节点是加入到 Azure AD DS 域的域。

![显示用于启用 HDInsight ID 代理的选项的关系图。](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

如果将具有以下属性的名为的新角色添加 `idbrokernode` 到模板的计算配置文件中，则将创建一个启用了 HDINSIGHT ID 代理节点的群集：

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

HDInsight 工具已更新为本机支持 OAuth。 使用这些工具进行群集的基于 OAuth 的新式访问。 HDInsight [IntelliJ 插件](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 可用于基于 Java 的应用程序，如 Scala。 适用于[Visual Studio Code 的 Spark 和 Hive 工具](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode)可用于 PySpark 和 hive 作业。 这些工具支持批处理和交互式作业。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>在 Azure AD DS 中在没有密码哈希的情况下进行 SSH 访问

|SSH 选项 |要考虑的因素 |
|---|---|
| 本地 VM 帐户 (例如 sshuser)  | 此帐户是在创建群集时提供的。 此帐户没有 Kerberos 身份验证。 |
| 仅限云的帐户 (例如， alice@contoso.onmicrosoft.com)  | 密码哈希在 Azure AD DS 中可用。 可以通过 SSH Kerberos 进行 kerberos 身份验证。 |
| 本地帐户 (例如， alice@contoso.com)  | 只有在 Azure AD DS 中提供了密码哈希，才能使用 SSH Kerberos 身份验证。 否则，此用户无法通过 SSH 连接到群集。 |

若要通过 SSH 连接到已加入域的 VM 或运行此 `kinit` 命令，必须提供密码。 SSH Kerberos 身份验证要求 Azure AD DS 中提供哈希。 如果只想将 SSH 用于管理方案，则可以创建一个仅限云的帐户，并使用它通过 SSH 连接到群集。 其他本地用户仍可以使用 Ambari 或 HDInsight 工具或 HTTP 基本身份验证，而无需在 Azure AD DS 中提供密码哈希。

如果你的组织未将密码哈希同步到 Azure AD DS，最佳做法是在 Azure AD 中创建一个仅限云的用户。 然后，在创建群集时将其分配为群集管理员，并将其用于管理目的。 可以使用它通过 SSH 获取对 Vm 的根访问权限。

若要解决身份验证问题，请参阅 [此指南](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)。

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>使用 OAuth 连接到使用 HDInsight ID 代理的 HDInsight 网关的客户端

在 HDInsight ID 代理安装中，可以更新连接到网关的自定义应用和客户端，以便首先获取所需的 OAuth 令牌。 按照 [本文档](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 中的步骤获取令牌，其中包含以下信息：

*   OAuth 资源 URI：`https://hib.azurehdinsight.net` 
*   AppId：7865c1d2-f040-46cc-875f-831a1ef6a28a
*   权限：（名称：Cluster.ReadWrite，id：8f89faa0-ffef-4007-974d-4989b39ad77d）

获取 OAuth 令牌后，将其在 HTTP 请求的授权标头中用于群集网关 (例如，https:// <clustername> -int.azurehdinsight.net) 。 Apache Livy API 的示例卷曲命令可能如下例所示：
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory 域服务配置具有企业安全性套餐的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
* [监视群集性能](../hdinsight-key-scenarios-to-monitor.md)
