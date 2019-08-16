---
title: 我的 WebJob 项目（Visual Studio Azure 存储连接服务）发生了什么情况？ | Microsoft Docs
description: 介绍使用 Visual Studio 连接服务连接到存储帐户后 Azure WebJob 项目中发生的情况
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510470"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>我的 WebJob 项目（Visual Studio Azure 存储连接服务）发生了什么情况？
## <a name="references-added"></a>已添加引用
Azure 存储 NuGet 包已添加到 Visual Studio 项目或在其中更新。  
此包添加了以下 .NET 引用：

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>已添加 Azure 存储的连接字符串
在项目的 App.config 文件中，已使用选定存储帐户的连接字符串和密钥更新 **AzureWebJobsStorage** 和 **AzureWebJobsDashboard** 条目。

有关详细信息，请参阅 [Azure WebJobs 文档资源](https://go.microsoft.com/fwlink/?linkid=390226)。

