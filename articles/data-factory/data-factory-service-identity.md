---
title: 数据工厂托管标识 |Microsoft Docs
description: 了解 Azure 数据工厂托管标识。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59260742"
---
# <a name="managed-identity-for-data-factory"></a>数据工厂的托管标识

本文可帮助你了解什么是托管的标识 （前身为托管服务标识/MSI） 的数据工厂和其工作原理。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

在创建数据工厂，可以工厂的同时创建托管的标识。 托管的标识是注册到 Azure Activity Directory 的托管应用程序，代表此特定数据工厂。

数据工厂托管的标识提供以下功能：

- [在 Azure 密钥保管库中存储凭据](store-credentials-in-key-vault.md)，在这种情况下数据工厂托管标识用于 Azure 密钥保管库身份验证。
- 连接器包括 [Azure Blob 存储](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure SQL 数据库](connector-azure-sql-database.md)和 [Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md)。
- [Web 活动](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>生成托管的标识

生成的数据工厂托管的标识，如下所示：

- 创建数据工厂通过时**Azure 门户或 PowerShell**、 托管始终会自动创建标识。
- 创建数据工厂通过时**SDK**、 托管将创建标识，仅当指定"Identity = new factoryidentity （）"中创建的工厂对象。 请参阅 [.NET 快速入门 - 创建数据工厂](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的示例。
- 创建数据工厂通过时**REST API**、 托管在请求正文中指定"identity"节，才会创建标识。 请参阅 [REST 快速入门 - 创建数据工厂](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的示例。

如果您发现您的数据工厂不会具有以下关联的托管的标识[检索托管的标识](#retrieve-managed-identity)指令，您可以显式生成一个通过使用标识发起程序以编程方式更新数据工厂：

- [生成托管的标识，使用 PowerShell](#generate-managed-identity-using-powershell)
- [生成托管的标识使用 REST API](#generate-managed-identity-using-rest-api)
- [生成托管的标识使用 Azure 资源管理器模板](#generate-managed-identity-using-an-azure-resource-manager-template)
- [生成托管的标识使用 SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 无法修改托管的标识。 更新已带有托管的标识的数据工厂不会产生任何影响，托管的标识将保持不变。
>- 如果更新已有一个托管的标识，而无需指定工厂对象中的"identity"参数，或者未在 REST 请求正文中指定"identity"节的数据工厂，将会出错。
>- 删除数据工厂时，将沿删除关联的托管的标识。

### <a name="generate-managed-identity-using-powershell"></a>生成托管的标识，使用 PowerShell

调用**集 AzDataFactoryV2**命令，则您看到正在生成新的"Identity"字段：

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

### <a name="generate-managed-identity-using-rest-api"></a>生成托管的标识使用 REST API

调用以下 API 并在请求正文中包含 "identity" 节：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**请求正文**：add "identity": { "type":"SystemAssigned" }。

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

**响应**： 自动创建托管的标识，并相应地填充"identity"节。

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>生成托管的标识使用 Azure 资源管理器模板

**模板**：add "identity": { "type":"SystemAssigned" }。

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

### <a name="generate-managed-identity-using-sdk"></a>生成托管的标识使用 SDK

结合 Identity=new FactoryIdentity() 调用数据工厂 create_or_update 函数。 使用 .NET 的示例代码：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>检索托管的标识

您可以检索从 Azure 门户或以编程方式托管的标识。 以下部分演示了一些示例。

>[!TIP]
> 如果看不到托管的标识，[生成托管的标识](#generate-managed-identity)通过更新您的工厂。

### <a name="retrieve-managed-identity-using-azure-portal"></a>检索托管的标识使用 Azure 门户

您可以找到从 Azure 门户的托管的标识信息-> 数据工厂-> 属性：

- 托管标识对象 ID
- 托管标识租户
- **托管标识应用程序 ID** > 复制此值

![检索托管的标识](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>检索托管的标识，使用 PowerShell

按以下方式获取特定的数据工厂时，将返回托管的标识主体 ID 和租户 ID:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

复制主体 ID，然后结合主体 ID 作为参数运行以下 Azure Active Directory 命令，获取用于授予访问权限的 **ApplicationId**：

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>后续步骤
请参阅以下主题，其中介绍何时以及如何使用数据工厂管理的标识：

- [在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)
- [使用 Azure 资源的托管标识身份验证从/向 Azure Data Lake Store 复制数据](connector-azure-data-lake-store.md)

请参阅[管理的标识的 Azure 资源概述](/azure/active-directory/managed-identities-azure-resources/overview)为基于 Azure 资源，哪些数据工厂托管标识的管理的标识的更多背景。 
