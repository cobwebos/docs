---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/06/2018
ms.author: genemi
ms.openlocfilehash: b3b7027e5ae252eb899d2a44d171ea9c9795ab54
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286039"
---
<!-- sql-database-connect-query-prerequisites-server-connection-info-includes.md 

## Get SQL server connection information
-->

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择左侧菜单中的“SQL 数据库”，然后在“SQL 数据库”页面上选择数据库。

1. 在数据库的“概述”页中，查看“服务器名称”旁边的完全限定的服务器名称。 要复制服务器名称，请将鼠标悬停在其上方，然后选择“复制”图标。  

![server-name](./media/sql-database-connect-query-prerequisites-server-connection-info-includes/server-name.png)

如果忘记了登录信息，请选择服务器名称以打开“SQL 服务器”页面。 可以在此处查看“服务器管理员”名称，如有必要，可以选择“重置密码”。