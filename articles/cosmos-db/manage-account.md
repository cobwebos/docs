---
title: 通过 Azure 门户管理 Cosmos DB 帐户 | Microsoft Docs
description: 了解如何通过 Azure 门户管理 Azure Cosmos DB 帐户。 查找有关使用 Azure 门户查看、复制、删除和访问帐户的指南。
keywords: Azure 门户, azure, Microsoft azure
services: cosmos-db
documentationcenter: ''
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: kirillg
ms.openlocfilehash: e5820cb17cfbaa15f10f24881f02a37aec617267
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>如何管理 Azure Cosmos DB 帐户
了解如何在 Azure 门户中设置全局一致性、使用密钥，以及删除 Azure Cosmos DB 帐户。

## <a id="consistency"></a>管理 Azure Cosmos DB 一致性设置
根据应用程序的语义选择正确的一致性级别。 通过阅读[使用一致性级别最大限度地提高 Azure Cosmos DB 中的可用性和性能][consistency]，自行熟悉 Azure Cosmos DB 中提供的一致性级别。 Azure Cosmos DB 在可用于数据库帐户的每个一致性级别提供一致性、可用性和性能保证。 使用非常一致性级别配置数据库帐户需要将数据局限在单个 Azure 区域，而不能使其全局可用。 另一方面，宽松的一致性级别 - 使用有限过期、会话或最终一致性可将任意数量的 Azure 区域与数据库帐户相关联。 以下简单步骤说明如何为数据库帐户选择默认的一致性级别。

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>指定 Azure Cosmos DB 帐户的默认一致性
1. 在 [Azure 门户](https://portal.azure.com/)中，访问 Azure Cosmos DB 帐户。
2. 在帐户页中，单击“默认一致性”。
3. 在“默认一致性”页中，选择新的一致性级别并单击“保存”。
    ![默认一致性会话][5]

## <a id="keys"></a>查看、复制和重新生成访问密钥与密码
创建 Azure Cosmos DB 帐户时，服务生成两个主访问密钥（或 MongoDB API 帐户的两个密码），可用于访问 Azure Cosmos DB 帐户时的身份验证。 提供两个访问密钥后，Azure Cosmos DB 支持在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 

在 [Azure 门户](https://portal.azure.com/)中，从“Azure Cosmos DB 帐户”页上的资源菜单访问“密钥”页，查看、复制和再生成用于访问 Azure Cosmos DB 帐户的访问密钥。 对于 MongoDB API 帐户，请从资源菜单访问**连接字符串**页，以查看、复制和重新生成用于访问帐户的密码。

![Azure 门户屏幕截图，密钥页](./media/manage-account/keys.png)

> [!NOTE]
> “密钥”页还包括可用来从[数据迁移工具](import-data.md)连接到用户帐户的主要和辅助连接字符串。
> 
> 

此页上还提供只读密钥。 读取和查询为只读操作，而创建、删除和替换则不是。

### <a name="copy-an-access-key-or-password-in-the-azure-portal"></a>在 Azure 门户中复制访问密钥或密码
在“密钥”页（或 MongoDB API 帐户的“连接字符串”页）上，单击要复制的密钥或密码右侧的“复制”按钮。

![在 Azure 门户的“密钥”页中，查看并复制访问密钥](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys-and-passwords"></a>重新生成访问密钥和密码
应定期更改 Azure Cosmos DB 帐户访问密钥（和 MongoDB API 帐户的密码），使连接更安全。 将分配两个访问密钥/密码，从而可以在使用一个访问密钥保持连接到 Azure Cosmos DB 帐户的同时，再生成另一个访问密钥。

> [!WARNING]
> 重新生成访问密钥会影响任何依赖于当前密钥的应用程序。 所有使用访问密钥访问 Azure Cosmos DB 帐户的客户端都必须更新为使用新密钥。
> 
> 

如果拥有使用 Azure Cosmos DB 帐户的应用程序或云服务，则再生成密钥将失去连接，除非滚动使用密钥。 以下步骤概述了滚动密钥/密码的过程。

1. 更新应用程序代码中的访问密钥以引用 Azure Cosmos DB 帐户的辅助访问密钥。
2. 再生成 Azure Cosmos DB 帐户的主访问密钥。 在 [Azure 门户](https://portal.azure.com/)中，访问 Azure Cosmos DB 帐户。
3. 在“Azure Cosmos DB 帐户”页上，单击“密钥”（或 MongoDB 帐户的“连接字符串”\**）。
4. 在“密钥”/“连接字符串”页上，单击“重新生成”按钮，并单击“确定”确认要生成新密钥。
    ![重新生成访问密钥](./media/manage-account/regenerate-keys.png)
5. 确认新的密钥可供使用后（大约在重新生成后的五分钟），请更新应用程序代码中的访问密钥以引用新的主访问密钥。
6. 重新生成辅助访问密钥。
   
    ![重新生成访问密钥](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 可能需要几分钟时间才能使用新生成的密钥来访问 Azure Cosmos DB 帐户。
> 
> 

## <a name="get-the-connection-string"></a>获取连接字符串
若要检索连接字符串，请执行以下操作： 

1. 在 [Azure 门户](https://portal.azure.com)中，访问 Azure Cosmos DB 帐户。
2. 在“资源”菜单中，单击“密钥”（或 MongoDB API 帐户的“连接字符串”）。
3. 单击“主连接字符串”  旁边的“复制”按钮  或单击“辅助连接字符串”  框。 

如果在 [Azure Cosmos DB 数据库迁移工具](import-data.md)中使用连接字符串，将数据库名称追加到连接字符串的末尾。 `AccountEndpoint=< >;AccountKey=< >;Database=< >`。

## <a id="delete"></a>删除 Azure Cosmos DB 帐户
要从 Azure 门户中删除不再使用的 Azure Cosmos DB 帐户，请右键单击该帐户名称，并单击“删除帐户”。

![如何在 Azure 门户中删除 Azure Cosmos DB 帐户](./media/manage-account/deleteaccount.png)

1. 在 [Azure 门户](https://portal.azure.com/)中，访问要删除的 Azure Cosmos DB 帐户。
2. 在“Azure Cosmos DB 帐户”页上，右键单击该帐户，并单击“删除帐户”。 
3. 在生成的确认页上，键入 Azure Cosmos DB 帐户名称，确认要删除该帐户。
4. 单击“删除” 按钮。

![如何在 Azure 门户中删除 Azure Cosmos DB 帐户](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>后续步骤
了解如何[开始使用 Azure Cosmos DB 帐户](http://go.microsoft.com/fwlink/p/?LinkId=402364)。

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
