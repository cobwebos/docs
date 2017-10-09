---
title: "在 Azure 数据工厂中加密凭据 | Microsoft Docs"
description: "了解如何在装有自承载集成运行时的计算机上加密和存储本地数据存储的凭据。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 8363187b7c8492e9386715324f87e70d7d8615af
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---

# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>在 Azure 数据工厂中加密本地数据存储的凭据
可以在装有自承载集成运行时的计算机上加密和存储本地数据存储（包含敏感信息的链接服务）的凭据。 

将包含凭据的 JSON 定义文件传递到 <br/>[**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) cmdlet，生成包含已加密凭据的输出 JSON 定义文件。 然后，使用更新的 JSON 定义来创建链接服务。

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
若要在本地自承载集成运行时中加密 JSON 有效负载中的敏感数据，请运行 **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** 并传递 JSON 有效负载。 此 cmdlet 可确保使用 DPAPI 加密凭据，并将其存储在自承载集成运行时节点本地。 可将输出的有效负载重定向到包含已加密凭据的另一个 JSON 文件（在本例中为“encryptedLinkedService.json”）。

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>使用包含已加密凭据的 JSON
现在，使用前一命令输出的包含已加密凭据的 JSON 文件来设置 **SqlServerLinkedService**。

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>后续步骤
有关数据移动安全注意事项的信息，请参阅[数据移动安全注意事项](data-movement-security-considerations.md)。


