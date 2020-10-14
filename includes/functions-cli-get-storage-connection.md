---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673378"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>检索 Azure 存储连接字符串

前面你已创建一个供函数应用使用的 Azure 存储帐户。 此帐户的连接字符串安全存储在 Azure 中的应用设置内。 将设置下载到 *local.settings.json* 文件中后，在本地运行函数时，可以在同一帐户中使用该连接写入存储队列。 

1. 在项目的根目录中运行以下命令（请将 `<app_name>` 替换为前一篇快速入门中所用的函数应用名称）。 此命令将覆盖该文件中的任何现有值。

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. 打开 *local.settings.json*，找到名为 `AzureWebJobsStorage` 的值，即存储帐户连接字符串。 在本文的其他部分，将使用名称 `AzureWebJobsStorage` 和该连接字符串。

> [!IMPORTANT]
> 由于 *local.settings.json* 包含从 Azure 下载的机密，因此请始终从源代码管理中排除此文件。 连同本地函数项目一起创建的 *.gitignore* 文件默认会排除该文件。