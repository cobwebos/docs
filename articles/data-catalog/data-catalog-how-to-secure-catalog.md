---
title: "如何保护对 Azure 数据目录的访问 | Microsoft Docs"
description: "本文说明如何保护数据目录及其数据资产。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 89346113c6231442beb1147c4c4fea524d03d909
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>如何保护对安全访问数据目录和数据资产的访问
> [!IMPORTANT]
> 此功能仅在 Azure 数据目录标准版中可用。

借助 Azure 数据目录，用户可指定有权访问数据目录的人员，以及他们可对目录中的元数据执行的操作（注册、批注、取得所有权）。 

## <a name="catalog-users-and-permissions"></a>目录用户和权限
为用户或组授予对数据目录的访问权并设置权限：

1. 在[数据目录主页](http://www.azuredatacatalog.com)的工具栏上，单击“设置”。

    ![数据目录 - 设置](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. 在设置页面中，展开“目录用户”部分。
    ![目录用户 - 添加](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. 单击 **“添加”**。
4. 在与目录关联的 Azure Active Directory (AAD) 中输入完全限定用户名或安全组名称。 若要添加多个用户或组，请使用逗号（“,”）作为分隔符。
    ![目录用户 - 用户或组](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. 在文本框外按 Enter 或 Tab。 
6.  请确认默认情况下为用户或组分配了所有权限（“批注”、“注册”和“取得所有权”）。 即：用户或组可以[注册数据资产]( data-catalog-how-to-register.md)、[批注数据资产]( data-catalog-how-to-annotate.md)和[取得数据资产的所有权]( data-catalog-how-to-manage.md)。 
    ![目录用户 - 默认权限](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  若仅向用户或组授予对目录的读取访问权限，请清除该用户或组对应的“批注”选项。 这样，用户或组无法对目录中的数据资产进行批注，但可以查看它们。 
8.  若要拒绝用户或组注册数据资产，请清除该用户或组对应的“注册”选项。
9.  若要拒绝用户取得数据资产的所有权，请清除该用户或组对应的“取得所有权”选项。 
10. 若要从目录用户中删除用户/组，请在列表底部单击该用户/组对应的 x。 
    ![目录用户 - 删除用户](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > 建议向目录用户添加安全组，而不是直接添加用户和分配权限。 然后将用户添加到与其角色和所需目录访问权限匹配的安全组。

## <a name="special-considerations"></a>特殊注意事项

- 分配给安全组的权限是累加式的。 例如，一个用户属于两个组。 一个组具有批注权限，另一个组没有批注权限。 则该用户具有批注权限。 
- 向用户显式分配的权限会替代分配给用户所属组的权限。 在前面的例子中，如果将用户显式添加到目录用户，并且不分配批注权限。 即使其所属的组具有批注权限，该用户也无法批注数据资产。

## <a name="next-steps"></a>后续步骤
- [Azure 数据目录入门](data-catalog-get-started.md)

