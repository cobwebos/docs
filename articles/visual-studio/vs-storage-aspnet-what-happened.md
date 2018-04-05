---
title: 我的 ASP.NET 项目发生了什么情况？ | Microsoft Docs
description: 介绍使用 Visual Studio 连接服务向 ASP.NET 项目添加 Azure 存储后会发生什么情况
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8f858dc5e3b28f5ea83af295e3dffe2c12fafc53
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>我的 ASP.NET 项目（Visual Studio Azure 存储连接服务）发生了什么情况？
## <a name="references-added"></a>已添加引用
Azure 存储 NuGet 包已添加到 Visual Studio 项目。  
此包添加了以下 .NET 引用：

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>已添加 Azure 存储的连接字符串
在项目的 web.config 文件中，已使用选定存储帐户的连接字符串和密钥创建了一个元素。

有关详细信息，请参阅 [ASP.NET](http://www.asp.net)。

