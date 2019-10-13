---
title: 我的 ASP.NET 5 项目发生了什么情况（Visual Studio 连接服务）
description: 介绍在 Visual Studio ASP.NET 5 项目中使用 Visual Studio 连接服务连接到 Azure 存储帐户后会发生什么情况
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300013"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>我的 ASP.NET 5 项目（Visual Studio Azure 存储连接服务）发生了什么情况？
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

此外，NuGet 包 **Microsoft.Framework.Configuration.Json** 已添加。

## <a name="connection-string-for-azure-storage-added"></a>已添加 Azure 存储的连接字符串
在项目的 config.json 文件中，已使用选定存储帐户的连接字符串和密钥创建了一个元素。

有关详细信息，请参阅 [ASP.NET 5](https://www.asp.net/vnext)。

