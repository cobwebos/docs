---
title: 数据工厂的托管标识
description: 了解 Azure 数据工厂的托管标识。
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: 7c1de2b6ef59efdaaed64fcf687fed0c834683c0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037590"
---
# <a name="managed-identity-for-data-factory"></a>数据工厂的托管标识

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文将帮助你了解什么是数据工厂的托管标识（以前称为托管服务标识/MSI）及其工作原理。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

创建数据工厂时，可在创建工厂的同时创建托管标识。 托管标识是注册到 Azure Active Directory 的托管应用程序，表示此特定数据工厂。

数据工厂的托管标识对以下功能有益：

- [在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)，在这种情况下，数据工厂托管标识用于 Azure Key Vault 身份验证。
- 连接器包括 [Azure Blob 存储](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure SQL 数据库](connector-azure-sql-database.md)和 [Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md)。
- [Web 活动](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>生成托管标识

数据工厂的托管标识是如下所述生成的：

- 通过 **Azure 门户或 PowerShell** 创建数据工厂时，始终会自动创建托管标识。
- 通过 **SDK** 创建数据工厂时，仅当在要创建的工厂对象中指定了“Identity = new FactoryIdentity()”时，才会创建托管标识。 请参阅 [.NET 快速入门 - 创建数据工厂](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的示例。
- 通过 **REST API** 创建数据工厂时，仅当在请求正文中指定了 "identity" 节时，才会创建托管标识。 请参阅 [REST 快速入门 - 创建数据工厂](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的示例。

如果发现数据工厂没有与以下[检索托管标识](#retrieve-managed-identity)说明相关的托管标识，可以使用标识发起程序以编程方式更新数据工厂，从而显式生成一个服务标识：

- [使用 PowerShell 生成托管标识](#generate-managed-identity-using-powershell)
- [使用 REST API 生成托管标识](#generate-managed-identity-using-rest-api)
- [使用 Azure 资源管理器模板生成托管标识](#generate-managed-identity-using-an-azure-resource-manager-template)
- [使用 SDK 生成托管标识](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 无法修改托管标识。 更新已带有托管标识的数据工厂不会产生任何影响，托管标识将保持不变。
>- 如果更新已带有托管标识的数据工厂，但未在工厂对象中指定 "identity" 参数，或者未在 REST 请求正文中指定 "identity" 节，将会收到错误。
>- 删除某个数据工厂时，会一并删除关联的托管标识。

### <a name="generate-managed-identity-using-powershell"></a>使用 PowerShell 生成托管标识

再次调用 **Set-AzDataFactoryV2** 命令，然后你会看到正在生成新的 "identity" 字段：

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>使用 REST API 生成托管标识

调用以下 API 并在请求正文中包含 "identity" 节：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**请求正文**：添加 "identity": { "type": "SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**响应**：自动创建托管标识并相应地填充 "identity" 部分。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板生成托管标识

**模版**：添加 "identity": { "type": "SystemAssigned" }。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>使用 SDK 生成托管标识

结合 Identity=new FactoryIdentity() 调用数据工厂 create_or_update 函数。 使用 .NET 的示例代码：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>检索托管标识

可以通过 Azure 门户或以编程方式检索托管标识。 以下部分演示了一些示例。

>[!TIP]
> 如果看不到托管标识，请通过更新工厂来[生成托管标识](#generate-managed-identity)。

### <a name="retrieve-managed-identity-using-azure-portal"></a>使用 Azure 门户检索托管标识

可以从 Azure 门户 -> 数据工厂 ->“属性”找到托管标识信息。

- 托管标识对象 ID
- 托管标识租户
- 托管标识应用程序 ID

当你创建链接服务时，托管标识信息也会显示，它支持托管标识身份验证，如 Azure Blob、Azure Data Lake Storage、Azure Key Vault 等。

授予权限时，请使用对象 ID 或数据工厂名称（作为托管标识名称）来查找此标识。

### <a name="retrieve-managed-identity-using-powershell"></a>使用 PowerShell 检索托管标识

获取特定的数据工厂时，会返回托管标识主体 ID 和租户 ID，如下所示。 使用 **PrincipalId** 授予访问权限：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

可以通过以下方法获取应用程序 ID：复制上面的主体 ID，然后以主体 ID 作为参数运行以下 Azure Active Directory 命令。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>使用 REST API 检索托管标识

当你获取特定数据工厂时，将返回托管标识主体 ID 和租户 ID，如下所示。

在请求中调用以下 API：

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**响应**：将获得如下所示的响应。 "标识" 部分会相应填充。

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> 若要从 ARM 模板检索托管标识，请在 ARM JSON 中添加 "**输出**" 部分：

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
请参阅以下主题，其中介绍了何时以及如何使用数据工厂托管标识：

- [在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)
- [使用 Azure 资源的托管标识身份验证从/向 Azure Data Lake Store 复制数据](connector-azure-data-lake-store.md)

有关数据工厂托管标识所基于的 Azure 资源的托管标识的更多背景信息，请参阅 [Azure 资源的托管标识概述](/azure/active-directory/managed-identities-azure-resources/overview)。 
