---
title: 我的云服务项目发生了什么情况？ | Microsoft Docs
description: 介绍使用 Visual Studio 连接服务连接到 Azure 存储帐户后云服务项目中会发生什么情况
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e823bc16c500dfee44312a774d5e3bd6622e5fae
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146330"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>我的云服务项目（Visual Studio Azure 存储连接服务）发生了什么情况？
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
已使用选定存储帐户的连接字符串和密钥创建了元素。 已对以下文件进行了修改：

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

