---
title: 使用托管标识将 Azure 流分析作业验证到 Azure Blob 存储输出 (预览)
description: 本文介绍如何使用托管标识对 azure 流分析作业进行 azure Blob 存储输出的身份验证。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 2bde1e8556fb1255e27595630e061f6b80870ce1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278689"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>使用托管标识将 Azure 流分析作业验证到 Azure Blob 存储输出 (预览)

用于输出到 Azure Blob 存储的[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)可用于 Azure 流分析作为预览。 这会使流分析作业直接访问存储帐户, 而不是使用连接字符串。 除了提高安全性外, 还可以使用此功能将数据写入 Azure 中的虚拟网络 (VNET) 中的存储帐户。

本文介绍如何通过 Azure 门户和 Azure 资源管理器部署, 为流分析作业的 Blob 输出启用托管标识。

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 门户创建流分析作业

1. 创建新的流分析作业, 或在 Azure 门户中打开现有作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识”。   确保选中 "使用系统分配的托管标识", 然后单击屏幕底部的 "**保存**" 按钮。

   ![配置流分析托管标识](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-enable-managed-identity.png)

2. 在 Azure Blob 存储输出接收器的 "输出属性" 窗口中, 选择 "身份验证模式" 下拉箭头, 然后选择 "**托管标识**"。 有关其他输出属性的信息, 请参阅[了解 Azure 流分析中的输出](./stream-analytics-define-outputs.md)。 完成后，单击“保存”。 

   ![配置 Azure Blob 存储输出](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 创建该作业后, 请参阅本文的[为流分析作业授予对存储帐户的访问权限](#give-the-stream-analytics-job-access-to-your-storage-account)部分。

## <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

使用 Azure 资源管理器可以完全自动部署流分析作业。 你可以使用 Azure PowerShell 或[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)部署资源管理器模板。 下面的示例使用 Azure CLI。


1. 可以通过在资源管理器模板的 resource 节中包含以下属性, 来创建具有托管标识的**streamanalytics-default-central-us/streamingjobs**资源:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   此属性告知 Azure 资源管理器创建和管理流分析作业的标识。 下面是一个示例模板示例资源管理器模板, 该模板部署启用了托管标识的流分析作业, 以及使用托管标识的 Blob 输出接收器:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    可以使用以下 Azure CLI 命令将上述作业部署到资源组**ExampleGroup** :

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 创建作业后, 可以使用 Azure 资源管理器检索作业的完整定义。

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    上述命令将返回如下所示的响应:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   记下作业的定义中的**principalId** , 它在 Azure Active Directory 中标识作业的托管标识, 并将在下一步中用于向流分析作业授予对存储帐户的访问权限。

3. 创建该作业后, 请参阅本文的[为流分析作业授予对存储帐户的访问权限](#give-the-stream-analytics-job-access-to-your-storage-account)部分。


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>向流分析作业授予对存储帐户的访问权限

你可以选择提供两个级别的访问权限, 以便为你的流分析作业提供:

1. **容器级别访问权限:** 此选项允许作业访问特定的现有容器。
2. **帐户级别访问权限:** 此选项提供对存储帐户的常规访问权限, 包括创建新容器的功能。

除非你需要以你的名义创建容器的作业, 否则应选择**容器级别访问权限**, 因为此选项会向作业授予所需的最低访问级别。 下面对 Azure 门户和命令行说明了这两个选项。

### <a name="grant-access-via-the-azure-portal"></a>通过 Azure 门户授予访问权限

#### <a name="container-level-access"></a>容器级别访问

1. 导航到存储帐户中的容器的配置窗格。

2. 选择左侧的 "**访问控制 (IAM)** "。

3. 在 "添加角色分配" 部分下, 单击 "**添加**"。

4. 在 "角色分配" 窗格中:

    1. 将**角色**设置为 "存储 Blob 数据参与者"
    2. 确保将 "**分配访问权限**" 设置为 "Azure AD 用户、组或服务主体"。
    3. 在搜索字段中键入流分析作业的名称。
    4. 选择流分析作业, 并单击 "**保存**"。

   ![授予容器访问权限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>帐户级别访问

1. 导航到存储帐户。

2. 选择左侧的 "**访问控制 (IAM)** "。

3. 在 "添加角色分配" 部分下, 单击 "**添加**"。

4. 在 "角色分配" 窗格中:

    1. 将**角色**设置为 "存储 Blob 数据参与者"
    2. 确保将 "**分配访问权限**" 设置为 "Azure AD 用户、组或服务主体"。
    3. 在搜索字段中键入流分析作业的名称。
    4. 选择流分析作业, 并单击 "**保存**"。

   ![授予帐户访问权限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>通过命令行授予访问权限

#### <a name="container-level-access"></a>容器级别访问

若要授予对特定容器的访问权限, 请使用 Azure CLI 运行以下命令:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>帐户级别访问

若要授予对整个帐户的访问权限, 请使用 Azure CLI 运行以下命令:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>启用 VNET 访问

在配置存储帐户的**防火墙和虚拟网络**时, 可以选择允许来自其他受信任的 Microsoft 服务的网络流量。 当流分析使用托管标识进行身份验证时, 它将提供请求源自受信任服务的证明。 下面说明了如何启用此 VNET 访问例外。

1.  在存储帐户的配置窗格中导航到 "防火墙和虚拟网络" 窗格。
2.  确保已启用 "允许受信任的 Microsoft 服务访问此存储帐户" 选项。
3.  如果启用此功能, 请单击 "**保存**"。

   ![启用 VNET 访问](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>限制
此功能的当前限制如下:

1. 经典 Azure 存储帐户。

2. 无 Azure Active Directory 的 Azure 帐户。

3. 不支持多租户访问。 为给定的流分析作业创建的服务主体必须位于创建该作业的 Azure Active Directory 租户中, 并且不能与驻留在不同 Azure Active Directory 租户中的资源一起使用。

4. 不支持[用户分配的标识](../active-directory/managed-identities-azure-resources/overview.md)。 这意味着用户无法进入其流分析作业使用的服务主体。 服务主体必须由 Azure 流分析生成。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](./stream-analytics-define-outputs.md)
* [Azure 流分析自定义 blob 输出分区](./stream-analytics-custom-path-patterns-blob-storage-output.md)
