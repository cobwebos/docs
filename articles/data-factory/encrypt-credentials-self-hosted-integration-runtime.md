---
title: 在 Azure 数据工厂中加密凭据 | Microsoft Docs
description: 了解如何在装有自承载集成运行时的计算机上加密和存储本地数据存储的凭据。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8e705a4430f6ccee847dc7d41ef80456a6dc4ea5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155134"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>在 Azure 数据工厂中加密本地数据存储的凭据
可以在装有自承载集成运行时的计算机上加密和存储本地数据存储（包含敏感信息的链接服务）的凭据。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

将包含凭据的 JSON 定义文件传递到 <br/>[**新 AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet，生成的加密凭据的输出 JSON 定义文件。 然后，使用更新的 JSON 定义来创建链接服务。

## <a name="author-sql-server-linked-service"></a>创作 SQL Server 链接服务
在任意文件夹中，创建包含以下内容的名为 **SqlServerLinkedService.json** 的 JSON 文件：  

将 `<servername>`、`<databasename>`、`<username>` 和 `<password>` 替换为 SQL Server 的值，然后保存文件。 另外，将 `<integration runtime name>` 替换为集成运行时的名称。 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>加密凭据
若要加密在本地自承载的集成运行时的 JSON 有效负载中的敏感数据，请运行**新建 AzDataFactoryV2LinkedServiceEncryptedCredential**，并传递 JSON 有效负载。 此 cmdlet 可确保使用 DPAPI 加密凭据，并将其存储在自承载集成运行时节点本地。 输出的有效负载包含对凭据加密的引用可以重定向到另一个 JSON 文件 （在此情况下 encryptedLinkedService.json）。

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>使用包含已加密凭据的 JSON
现在，使用前一命令输出的包含已加密凭据的 JSON 文件来设置 **SqlServerLinkedService**。

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>后续步骤
有关数据移动安全注意事项的信息，请参阅[数据移动安全注意事项](data-movement-security-considerations.md)。

