---
title: "使用 Excel 连接到 Azure Analysis Services | Microsoft Docs"
description: "了解如何使用 Excel 连接到 Azure Analysis Services 服务器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: ff96602642c56a3cd02aeada056c059573707731
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017


---
# <a name="connect-with-excel"></a>使用 Excel 进行连接

在 Azure 中创建服务器并向其部署表格模型后，便可以连接并开始浏览数据。


## <a name="connect-in-excel"></a>在 Excel 中连接

可通过使用 Excel 2016 中的“获取数据”在 Excel 中连接到服务器。 不支持使用 Power Pivot 中的“导入表向导”进行连接。 

**在 Excel 2016 中连接**

1. 在 Excel 2016 的“数据”功能区上，单击“获取外部数据” > “从其他源获取” > “从 Analysis Services 获取”。

2. 在“数据连接向导”的“服务器名称”中，输入服务器名称，包括协议和 URI。 然后，在“登录凭据”中，选择“使用以下用户名和密码”，然后键入组织的用户名（例如 nancy@adventureworks.com）和密码。

    ![“从 Excel 中连接”登录](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. 在“选择数据库和表”中，选择数据库和模型或透视，然后单击“完成”。
   
    ![“从 Excel 中连接”选择模型](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>另请参阅
[客户端库](analysis-services-data-providers.md)   
[管理服务器](analysis-services-manage.md)     



