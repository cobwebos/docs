---
title: 配置 Postman 以便进行 Azure 媒体服务 REST API 调用
description: 了解如何为媒体服务 REST API 调用配置 Postman。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 5d0b5a57f3fe587a06a102c958b17dbf2a73225c
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805136"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>配置 Postman 以便进行媒体服务 REST API 调用  

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，请参阅[从 v2 到 v3 迁移指南](../latest/migrate-from-v2-to-v3.md)

本教程介绍如何配置 **Postman**，以便可以使用它调用 Azure 媒体服务 (AMS) REST API。 本教程介绍如何将环境和集合文件导入到 **Postman**。 集合包含调用 Azure 媒体服务 (AMS) REST API 的 HTTP 请求的分组定义。 环境文件包含集合使用的变量。

在介绍如何使用 Azure 媒体服务 REST API 实现各种任务的文章中使用了此环境和集合。

## <a name="prerequisites"></a>系统必备

- 安装 [Postman](https://www.getpostman.com/) REST 客户端，以便执行一些 AMS REST 教程中所示的 REST API。 

    我们使用的是 **Postman**，但任何 REST 工具都适用。 其他替代工具包括：带有 REST 插件的 **Visual Studio Code** 或 **Telerik Fiddler**。 

## <a name="configure-the-environment"></a>配置环境 

1. 创建一个包含 AMS 教程中所用环境变量的 .json 文件。 命名该文件（例如，**AzureMediaServices.postman_environment.json**）。 打开该文件并从[此代码清单](postman-environment.md)粘贴用于定义 Postman 环境的代码。 
2. 打开 **Postman**。
3. 在屏幕的右侧，选择“管理环境”选项。

    ![上传文件](./media/media-services-rest-upload-files/postman-create-env.png)
4. 从“管理环境”对话框中，单击“导入”。
5. 浏览并选择 **AzureMediaServices.postman_environment.json** 文件。
6. 添加 **AzureMedia** 环境。
7. 关闭对话框。
8. 选择 **AzureMedia** 环境。

    ![上传文件](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>配置集合

1. 创建一个包含 **Postman** 集合的 .json 文件，该集合包含将文件上传到媒体服务所需的所有操作。 命名该文件（例如，**AzureMediaServicesOperations.postman_collection.json**）。 打开该文件并从[此代码清单](postman-collection.md)粘贴用于定义 **Postman** 集合的代码。
2. 单击“导入”导入该集合文件。
3. 选择 **AzureMediaServicesOperations.postman_collection.json** 文件。

    ![上传文件](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>后续步骤

查阅[上传资产](media-services-rest-upload-files.md)一文。  
