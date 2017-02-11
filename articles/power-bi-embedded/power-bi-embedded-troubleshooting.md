---
title: "Microsoft Power BI Embedded 预览版疑难解答"
description: "Microsoft Power BI Embedded 预览版疑难解答"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: c8aee652-ed8b-4c66-9c63-f798b7a655b4
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/06/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f4cf4a04e7a921f8e11c94f74d53ac80c231933


---
# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Microsoft Power BI Embedded 预览版疑难解答
本文提供了有关如何解决 **Power BI Embedded** 问题的信息。

<a name="connection-string"/>

## <a name="setting-sql-server-connection-strings"></a>设置 SQL Server 连接字符串
若要设置 SQL Server 连接字符串，请遵循以下特定格式。 下面列举了一个 SQL Server 连接字符串的示例。

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

若要了解有关 SQL Server 连接字符串的详细信息，请参阅以下文章：

* [SQL Server 连接字符串](https://msdn.microsoft.com/library/jj653752.aspx)
* [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>

## <a name="setting-credentials"></a>设置凭据
如果你具有用于开发或过渡环境的凭据（例如用户名和密码），建议更新此凭据，以匹配生产解决方案。

## <a name="see-also"></a>另请参阅
* [示例入门](power-bi-embedded-get-started-sample.md)
* [什么是 Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)




<!--HONumber=Nov16_HO3-->


