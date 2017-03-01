---
title: "使用 cURL 和 REST 创建 Azure HDInsight (Hadoop) | Microsoft Docs"
description: "了解如何使用 cURL、Azure Resource Manager 模板和 Azure REST API 创建 HDInsight 群集。 可以指定群集类型（Hadoop、HBase 或 Storm），或使用脚本来安装自定义组件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 98be5893-2c6f-4dfa-95ec-d4d8b5b7dcb5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: c8ed0760650f7fb3c85eef4ec7f72c60bb0efd00
ms.lasthandoff: 02/21/2017


---
# <a name="create-hdinsight-clusters-using-curl-and-the-azure-rest-api"></a>使用 cURL 和 Azure REST API 创建 HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

了解如何使用 Azure Resource Manager 模板和 Azure REST API 创建 HDInsight 群集。

Azure REST API 允许你对托管在 Azure 平台中的服务执行管理操作，包括创建新资源（例如 HDInsight 群集）。

> [!IMPORTANT]
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **Azure CLI 2.0**（预览版）。 Azure CLI 用于创建服务主体，为针对 Azure REST API 的请求生成身份验证令牌时需要使用此主体。 有关 Azure CLI 2.0 预览版的详细信息，请参阅 [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。

* **cURL**。 可通过程序包管理系统获取此实用工具，也可以从 [http://curl.haxx.se/](http://curl.haxx.se/) 下载此实用工具。

  > [!NOTE]
  > 如果使用 PowerShell 运行本文档中的命令，则必须先删除默认创建的 `curl` 别名。 此别名使用 Invoke-WebRequest 而不是 cURL。 如果不删除此别名，则可能会收到一些与本文档中使用的命令有关的错误。
  >
  > 若要删除此别名，请从 PowerShell 提示符使用以下命令：
  >
  > `Remove-item alias:curl`
  >
  > 删除别名后，你应该能够使用系统上安装的 cURL 版本。

### <a name="access-control-requirements"></a>访问控制要求

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-template"></a>创建模板

Azure Resource Manager 模板是描述**资源组**及其包含的所有资源（例如 HDInsight）的 JSON 文档此基于模板的方法可以在一个模板中定义需要用于 HDInsight 的资源。

下面是来自 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) 的模板与参数文件的组合形式，它将创建基于 Linux 的群集，并使用密码来保护 SSH 用户帐户。

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "location": {
                       "type": "string",
                       "allowedValues": ["Central US",
                       "East Asia",
                       "East US",
                       "Japan East",
                       "Japan West",
                       "North Europe",
                       "South Central US",
                       "Southeast Asia",
                       "West Europe",
                       "West US"],
                       "metadata": {
                           "description": "The location where all azure resources are deployed."
                       }
                   },
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[parameters('location')]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.5",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "Standard_D3"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "location": {
                   "value": "North Europe"
               },
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

本文档中的步骤会使用此示例。 将 Parameters 部分中的示例值替换为用于群集的值。

> [!IMPORTANT]
> 此模板对 HDInsight 群集使用默认数目（4 个）的辅助角色节点。 如果你计划使用 32 个以上的辅助角色节点，则必须选择至少具有 8 个核心和 14 GB ram 的头节点大小。
>
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅

请按照 [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)中所述的步骤操作，并使用 `az login` 命令连接到你的订阅。

## <a name="create-a-service-principal"></a>创建服务主体

> [!NOTE]
> 这些步骤是[使用 Azure CLI 创建服务主体以访问资源](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md#create-service-principal-with-password)文档的“使用密码创建服务主体”部分的缩减版本。 这些步骤创建用于向 Azure REST API 进行身份验证的服务主体。

1. 从命令行使用以下命令列出 Azure 订阅。

   ```bash
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    在列表中，选择要使用的订阅并记下 **Subscription_ID** 和 __Tenant_ID__ 列。 保存这些值。

2. 使用以下命令在 Azure Active Directory 中创建应用程序。

   ```bash
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    将 `--display-name`、`--homepage` 和 `--identifier-uris` 的值替换为你自己的值。 为新的 Active Directory 条目提供密码。

   > [!NOTE]
   > `--home-page` 和 `--identifier-uris` 值无需引用在 Internet 上承载的实际网页。 它们必须是唯一的 URI。

   此命令返回的值是新应用程序的__应用 ID__。 保存此值。

3. 通过以下命令使用**应用 ID** 创建服务主体。

   ```bash
   az ad sp create --id <App ID> --query 'objectId'
   ```

     此命令返回的值是__对象 ID__。 保存此值。

4. 使用**对象 ID** 值向服务主体分配**所有者**角色。 使用前面获取的**订阅 ID**。

   ```bash
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>获取身份验证令牌

使用以下命令检索身份验证令牌：

    curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
    -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
    -H "Content-Type: application/x-www-form-urlencoded" \
    --data-urlencode "client_id=AppID" \
    --data-urlencode "grant_type=client_credentials" \
    --data-urlencode "client_secret=password" \
    --data-urlencode "resource=https://management.azure.com/"

    Replace **TenantID**, **AppID**, and **password** with the values obtained or used previously.

    If this request is successful, you receive a 200 series response and the response body contains a JSON document.

    The JSON document returned by this request contains an element named **access_token**. The value of **access_token** is used to authentication requests to the REST API.

   ```json
   {
       "token_type":"Bearer",
       "expires_in":"3599",
       "expires_on":"1463409994",
       "not_before":"1463406094",
       "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
   }
   ```

## <a name="create-a-resource-group"></a>创建资源组

使用以下命令创建资源组。

* 将 **SubscriptionID** 替换为创建服务主体时收到的订阅 ID。
* 将 **AccessToken** 替换为在上一步骤中收到的访问令牌。
* 将 **DataCenterLocation** 替换为要在其中创建资源组和资源的数据中心。 例如，“美国中南部”。
* 将 **ResourceGroupName** 替换为要用于此组的名称：

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

如果此请求成功，你会收到 200 系列响应，且响应正文包含一个 JSON 文档，其中包含有关组的信息。 `"provisioningState"` 元素包含 `"Succeeded"` 的值。

## <a name="create-a-deployment"></a>创建部署

使用以下命令将模板部署到资源组。

* 将 **SubscriptionID** 和 **AccessToken** 替换为前面使用的值。
* 将 **ResourceGroupName** 替换在上一部分中创建的资源组名称。
* 将 **DeploymentName** 替换为要用于此部署的名称。

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]
> 如果将模板保存到了文件中，则可以使用以下命令而不是 `-d "{ template and parameters}"`：
>
> `--data-binary "@/path/to/file.json"`

如果此请求成功，你会收到 200 系列响应，且响应正文包含一个 JSON 文档，其中包含有关部署操作的信息。

> [!IMPORTANT]
> 部署已提交，但目前尚未完成。 部署通常需要大约 15 分钟才能完成。

## <a name="check-the-status-of-a-deployment"></a>检查部署状态

若要检查部署的状态，请使用以下命令：

* 将 **SubscriptionID** 和 **AccessToken** 替换为前面使用的值。
* 将 **ResourceGroupName** 替换在上一部分中创建的资源组名称。

```bash
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

此命令会返回包含有关部署操作的信息的 JSON 文档。 `"provisioningState"` 元素包含部署的状态。 如果这值 `"Succeeded"`，则部署已成功完成。

## <a name="next-steps"></a>后续步骤

成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集。

### <a name="hadoop-clusters"></a>Hadoop 群集

* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 群集

* [HBase on HDInsight 入门](hdinsight-hbase-tutorial-get-started-linux.md)
* [为 HBase on HDInsight 开发 Java 应用程序](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 群集

* [为 Storm on HDInsight 开发 Java 拓扑](hdinsight-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 组件](hdinsight-storm-develop-python-topology.md)
* [使用 Storm on HDInsight 部署和监视拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)

