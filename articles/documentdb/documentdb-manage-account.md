---
title: "通过 Azure 门户管理 DocumentDB 帐户 | Microsoft Docs"
description: "了解如何通过 Azure 门户管理你的 DocumentDB 帐户。 查找有关使用 Azure 门户查看、复制、删除和访问帐户的指南。"
keywords: "Azure 门户、documentdb、azure、Microsoft azure"
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: e82d677c9d53ec5d28a40a7def4b911081765ca5
ms.openlocfilehash: 0a249d63df35f9c3334a658672777e9b1359265e


---
# <a name="how-to-manage-a-documentdb-account"></a>如何管理 DocumentDB 帐户
了解如何在 Azure 门户中设置全局一致性、使用密钥，以及删除 DocumentDB 帐户。

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>管理 DocumentDB 一致性设置
根据应用程序的语义选择正确的一致性级别。 用户应通过阅读[使用一致性级别最大限度地提高 DocumentDB 中的可用性和性能][consistency]，自行熟悉 DocumentDB 中提供的一致性级别。 DocumentDB 在可用于数据库帐户的每个一致性级别提供一致性、可用性和性能保证。 使用非常一致性级别配置数据库帐户需要将数据局限在单个 Azure 区域，而不能使其全局可用。 另一方面，宽松的一致性级别 - 受限停滞、会话或最终一致性可让你将任意数量的 Azure 区域与你的数据库帐户相关联。 以下简单步骤说明如何为数据库帐户选择默认的一致性级别。 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>指定 DocumentDB 帐户的默认一致性
1. 在 [Azure 门户](https://portal.azure.com/)中，访问 DocumentDB 帐户。
2. 在帐户边栏选项卡中，单击“默认一致性”。
3. 在“默认一致性”边栏选项卡中，选择新的一致性级别并单击“保存”。
    ![默认一致性会话][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>查看、复制和重新生成访问密钥
当你创建 DocumentDB 帐户时，服务生成两个主访问密钥，用于访问 DocumentDB 帐户时的身份验证。 提供两个访问密钥后，DocumentDB 支持在不中断你的 DocumentDB 帐户连接的情况下重新生成密钥。 

在 [Azure 门户](https://portal.azure.com/)中，从“DocumentDB 帐户”边栏选项卡上的资源菜单访问“密钥”边栏选项卡，查看、复制和重新生成用于访问 DocumentDB 帐户的访问密钥。

![Azure 门户屏幕截图，密钥边栏选项卡](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> “密钥”边栏选项卡还包括可用来从[数据迁移工具](documentdb-import-data.md)连接到用户帐户的主要和辅助连接字符串。
> 
> 

此边栏选项卡上还提供只读密钥。 读取和查询为只读操作，而创建、删除和替换则不是。

### <a name="copy-an-access-key-in-the-azure-portal"></a>在 Azure 门户中复制访问密钥
在“密钥”边栏选项卡中，单击要复制的密钥右侧的“复制”按钮。

![在 Azure 门户中查看并复制访问密钥，密钥边栏选项卡](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>重新生成访问密钥
你应定期将访问密钥更改为你的 DocumentDB 帐户，使你的连接更安全。 分配了两个访问密钥，你可以使用一个访问密钥保持与 DocumentDB 帐户的连接，同时，你可以重新生成另一个访问密钥。

> [!WARNING]
> 重新生成访问密钥会影响任何依赖于当前密钥的应用程序。 所有使用访问密钥访问 DocumentDB 帐户的客户端都必须更新为使用新密钥。
> 
> 

如果你具有使用 DocumentDB 帐户的应用程序或云服务，则重新生成密钥将失去连接，除非你滚动使用密钥。 以下步骤概述了滚动密钥的过程。

1. 更新应用程序代码中的访问密钥以引用 DocumentDB 帐户的辅助访问密钥。
2. 为你的 DocumentDB 帐户重新生成主访问密钥。 在 [Azure 门户](https://portal.azure.com/)中，访问 DocumentDB 帐户。
3. 在“DocumentDB 帐户”边栏选项卡中，单击“密钥”。
4. 在“密钥”边栏选项卡上，单击“重新生成”按钮，然后单击“确定”确认要生成新密钥。
    ![重新生成访问密钥](./media/documentdb-manage-account/regenerate-keys.png)
5. 当你确认新的密钥可供使用后（大约在重新生成后的 5 分钟），请更新应用程序代码中的访问密钥以引用新的主访问密钥。
6. 重新生成辅助访问密钥。
   
    ![重新生成访问密钥](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 可能需要几分钟时间才能使用新生成的密钥来访问你的 DocumentDB 帐户。
> 
> 

## <a name="get-the--connection-string"></a>获取连接字符串
若要检索连接字符串，请执行以下操作： 

1. 在 [Azure 门户](https://portal.azure.com)中，访问 DocumentDB 帐户。
2. 在资源菜单中，单击“密钥”。
3. 单击“主连接字符串”  旁边的“复制”按钮  或单击“辅助连接字符串”  框。 

如果在 [DocumentDB 数据库迁移工具](documentdb-import-data.md)中使用连接字符串，将数据库名称追加到连接字符串的末尾。 `AccountEndpoint=< >;AccountKey=< >;Database=< >`。

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> 删除 DocumentDB 帐户
若要从 Azure 门户中删除不再使用的 DocumentDB 帐户，请右键单击该帐户名称，然后单击“删除帐户”。

![如何在 Azure 门户中删除 DocumentDB 帐户](./media/documentdb-manage-account/deleteaccount.png)

1. 在 [Azure 门户](https://portal.azure.com/)中，访问要删除的 DocumentDB 帐户。
2. 在“DocumentDB 帐户”边栏选项卡上，右键单击该帐户，然后单击“删除帐户”。 
3. 在生成的确认边栏选项卡中，键入 DocumentDB 帐户名称以确认要删除该帐户。
4. 单击“删除” 按钮。

![如何在 Azure 门户中删除 DocumentDB 帐户](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>后续步骤
了解如何[开始使用 DocumentDB 帐户](http://go.microsoft.com/fwlink/p/?LinkId=402364)。

若要了解更多有关 DocumentDB 的信息，请参阅 [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409) 上的 Azure DocumentDB 文档。

<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Feb17_HO1-->


