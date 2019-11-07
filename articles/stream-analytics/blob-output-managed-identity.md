---
title: 用托管标识验证 blob 输出 Azure 流分析
description: 本文介绍如何使用托管标识在 Azure Blob 存储输出中对 Azure 流分析作业进行身份验证。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1854a99cac3ead386f38c1c7fe27a56b672f365c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664300"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>使用托管标识在 Azure Blob 存储输出（预览版）中对 Azure 流分析作业进行身份验证

输出到 Azure Blob 存储的[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)在 Azure 流分析中以预览版提供。 这样，流分析作业便可以直接访问存储帐户，而无需使用连接字符串。 除了提高安全性以外，此功能还可让你将数据写入到 Azure 上的虚拟网络 (VNET) 中的存储帐户。

本文介绍如何通过 Azure 门户以及通过 Azure 资源管理器部署，为流分析作业的 Blob 输出启用托管标识。

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 门户创建流分析作业

1. 在 Azure 门户中创建新的流分析作业，或打开现有的作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识”。 确保选择“使用系统分配的托管标识”，然后单击屏幕底部的“保存”按钮。

   ![配置流分析托管标识](./media/common/stream-analytics-enable-managed-identity.png)

2. 在 Azure Blob 存储输出接收器的输出属性窗口中，选择“身份验证模式”下拉列表并选择“托管标识”。 有关其他输出属性的信息，请参阅[了解 Azure 流分析的输出](./stream-analytics-define-outputs.md)。 完成后，单击“保存”。

   ![配置 Azure Blob 存储输出](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 创建作业后，请参阅本文的[为流分析作业授予对存储帐户的访问权限](#give-the-stream-analytics-job-access-to-your-storage-account)部分。

## <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

使用 Azure 资源管理器可以完全自动化流分析作业的部署。 可以使用 Azure PowerShell 或 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 部署资源管理器模板。 以下示例使用 Azure CLI。


1. 可以通过在资源管理器模板的 resource 节中包含以下属性，来创建带有托管标识的 **Microsoft.StreamAnalytics/streamingjobs** 资源：

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   此属性告知 Azure 资源管理器为流分析作业创建和管理标识。 以下示例资源管理器模板部署一个启用了托管标识的流分析作业，以及一个使用托管标识的 Blob 输出接收器：

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

    可使用以下 Azure CLI 命令将上述作业部署到资源组 **ExampleGroup**：

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 创建作业后，可以使用 Azure 资源管理器检索该作业的完整定义。

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    以上命令将返回如下所示的响应：

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

   请记下作业定义中的 **principalId**，它用于在 Azure Active Directory 中标识该作业的托管标识，在下一步骤中，将使用它来为流分析作业授予对存储帐户的访问权限。

3. 创建作业后，请参阅本文的[为流分析作业授予对存储帐户的访问权限](#give-the-stream-analytics-job-access-to-your-storage-account)部分。


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>为流分析作业授予对存储帐户的访问权限

可以选择为流分析作业授予两种级别的访问权限：

1. **容器级访问权限**：使用此选项可为作业授予对现有特定容器的访问权限。
2. **帐户级访问权限**：使用此选项可为作业授予对存储帐户的一般访问权限，包括创建新容器的能力。

除非你需要作业代表你创建容器，否则应选择“容器级访问权限”，因为此选项将为作业授予所需的最低访问权限级别。 下面将会解释这两个选项在 Azure 门户和命令行中的用法。

### <a name="grant-access-via-the-azure-portal"></a>通过 Azure 门户授予访问权限

#### <a name="container-level-access"></a>容器级访问权限

1. 导航到存储帐户中的容器配置窗格。

2. 在左侧选择“访问控制(IAM)”。

3. 在“添加角色分配”部分下，单击“添加”。

4. 在角色分配窗格中：

    1. 将“角色”设置为“存储 Blob 数据参与者”
    2. 确保“将访问权限分配给”下拉列表设置为“Azure AD 用户、组或服务主体”。
    3. 在搜索字段中键入流分析作业的名称。
    4. 选择你的流分析作业，然后单击“保存”。

   ![授予容器访问权限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>帐户级访问权限

1. 导航到存储帐户。

2. 在左侧选择“访问控制(IAM)”。

3. 在“添加角色分配”部分下，单击“添加”。

4. 在角色分配窗格中：

    1. 将“角色”设置为“存储 Blob 数据参与者”
    2. 确保“将访问权限分配给”下拉列表设置为“Azure AD 用户、组或服务主体”。
    3. 在搜索字段中键入流分析作业的名称。
    4. 选择你的流分析作业，然后单击“保存”。

   ![授予帐户访问权限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>通过命令行授予访问权限

#### <a name="container-level-access"></a>容器级访问权限

若要授予对特定容器的访问权限，请使用 Azure CLI 运行以下命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>帐户级访问权限

若要授予对整个帐户的访问权限，请使用 Azure CLI 运行以下命令：

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>启用 VNET 访问权限

配置存储帐户的**防火墙和虚拟网络**时，可以视需要允许来自其他受信任 Microsoft 服务的网络流量。 当流分析使用托管标识进行身份验证时，它会提供该请求源自受信任服务的证明。 下面是有关启用此 VNET 访问权限例外的说明。

1.  在存储帐户的配置窗格中导航到“防火墙和虚拟网络”窗格。
2.  确保启用“允许受信任的 Microsoft 服务访问此存储帐户”选项。
3.  如果已启用此选项，请单击“保存”。

   ![启用 VNET 访问权限](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>限制
下面是此功能的当前限制：

1. 经典 Azure 存储帐户。

2. 不包含 Azure Active Directory 的 Azure 帐户。

3. 不支持多租户访问。 为给定流分析作业创建的服务主体必须驻留在创建了该作业的同一 Azure Active Directory 租户中，而不可用于驻留在其他 Azure Active Directory 租户中的资源。

4. 不支持[用户分配的标识](../active-directory/managed-identities-azure-resources/overview.md)。 这意味着，用户不能输入自己的供流分析作业使用的服务主体。 服务主体必须由 Azure 流分析生成。

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](./stream-analytics-define-outputs.md)
* [Azure 流分析自定义 Blob 输出分区](./stream-analytics-custom-path-patterns-blob-storage-output.md)
