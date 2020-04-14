---
title: 使用托管标识对 Azure 流分析作业进行身份验证，以为 BI 输出提供支持
description: 本文介绍如何使用托管标识对 Azure 流分析作业进行身份验证以将 BI 输出提供支持。
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261408"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>使用托管标识对 Azure 流分析作业进行身份验证，以为 BI 提供支持

用于输出到 Power BI 的[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)使流分析作业直接访问 Power BI 帐户中的工作区。 此功能允许完全自动化流分析作业的部署，因为用户不再需要通过 Azure 门户以交互方式登录到 Power BI。 此外，现在更好地支持写入 Power BI 的长运行作业，因为您不需要定期重新授权该作业。

本文演示如何通过 Azure 门户和 Azure 资源管理器部署为流分析作业的 Power BI 输出启用托管标识。

## <a name="prerequisites"></a>先决条件

使用此功能需要以下内容：

- 具有[Pro 许可证](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)的 Power BI 帐户。

- Power BI 帐户中的升级工作区。 有关详细信息，请参阅 Power BI 的[此功能公告](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)。

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 门户创建流分析作业

1. 在 Azure 门户中创建新的流分析作业，或打开现有的作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识”。******** 确保选择"使用系统分配的托管标识"，然后选择屏幕底部的 **"保存**"按钮。

   ![配置流分析托管标识](./media/common/stream-analytics-enable-managed-identity.png)

2. 在配置输出之前，请按照本文的[Power BI 工作区的"授予流分析作业访问权限](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)"部分的说明，为流分析作业授予对 Power BI 工作区的访问权限。

3. 导航到流分析作业的 **"输出"** 部分，选择 **"添加**"，然后选择 **"Power BI**"。 然后，选择 **"授权"** 按钮，然后使用 Power BI 帐户登录。

   ![使用 Power BI 帐户授权](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 获得授权后，将填充下拉列表，并填充您有权访问的所有工作区。 选择您在上一步中授权的工作区。 然后选择**托管标识**作为"身份验证模式"。 最后，选择"**保存**"按钮。

   ![使用托管标识配置电源 BI 输出](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

Azure 资源管理器允许您完全自动部署流分析作业。 可以使用 Azure PowerShell 或 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 部署资源管理器模板。 以下示例使用 Azure CLI。


1. 您可以通过在资源管理器模板的资源部分中包括以下属性来创建具有托管标识的**Microsoft.StreamAnalytics/流式作业**资源：

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   此属性告知 Azure 资源管理器为流分析作业创建和管理标识。 下面是一个示例资源管理器模板，该模板部署了启用托管标识的流分析作业和使用托管标识的 Power BI 输出接收器：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    使用以下 Azure CLI 命令将上述作业部署到资源组**示例组**：

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 创建作业后，使用 Azure 资源管理器检索作业的完整定义。

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    以上命令将返回如下所示的响应：

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    如果您计划使用 Power BI 的 REST API 将流分析作业添加到 Power BI 工作区，请记下返回的"主体 Id"。

3. 创建作业后，请继续"[为流分析作业提供对](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)本文 Power BI 工作区部分的访问权限"。


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>为流分析作业授予对 Power BI 工作区的访问权限

现在，流分析作业已创建，可以授予它对 Power BI 工作区的访问权限。

### <a name="use-the-power-bi-ui"></a>使用电源 BI UI

   > [!Note]
   > 要使用 UI 将流分析作业添加到 Power BI 工作区，还必须在 Power BI 管理门户中的**开发人员设置**中启用服务主体访问。 有关详细信息[，请参阅使用服务主体入门](https://docs.microsoft.com/power-bi/developer/embed-service-principal)。

1. 导航到工作区的访问设置。 有关详细信息，请参阅本文：[授予对工作区的访问权限](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)。

2. 在文本框中键入流分析作业的名称，然后选择 **"参与者**"作为访问级别。

3. 选择 **"添加**并关闭窗格"。

   ![将流分析作业添加到 Power BI 工作区](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>使用电源 BI 电源外壳 cmdlet

1. 安装电源 BI`MicrosoftPowerBIMgmt`电源外壳 cmdlet。

   > [!Important]
   > 请确保您使用的是 1.0.821 版或更高版本的 cmdlet。

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. 登录到电源 BI。

```powershell
Login-PowerBI
```

3. 将流分析作业添加为工作区的"参与者"。

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>使用电源 BI REST API

流分析作业还可以直接使用"添加组用户"REST API 作为参与者添加到工作区。 此 API 的完整文档可在此处找到：[组 - 添加组用户](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser)。

**示例请求**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
请求正文
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>限制
以下是此功能的限制：

- 不支持经典 Power BI 工作区。

- 不包含 Azure Active Directory 的 Azure 帐户。

- 不支持多租户访问。 为给定流分析作业创建的服务主体必须驻留在创建了该作业的同一 Azure Active Directory 租户中，而不可用于驻留在其他 Azure Active Directory 租户中的资源。

- 不支持[用户分配的标识](../active-directory/managed-identities-azure-resources/overview.md)。 这意味着您无法输入自己的服务主体，以便其流分析作业使用。 服务主体必须由 Azure 流分析生成。

## <a name="next-steps"></a>后续步骤

* [Power BI 仪表板与 Azure 流分析的集成](./stream-analytics-power-bi-dashboard.md)
* [了解 Azure 流分析的输出](./stream-analytics-define-outputs.md)
