---
title: 导入或导出数据与 Azure 应用程序配置 |Microsoft Docs
description: 了解如何导入或导出数据到或从 Azure 应用程序配置
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 851a8705b3cfa9c88e369af7b961a653dee8fd7a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959844"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用程序配置支持的数据导入和导出操作。 这样，您可以使用大容量以及您的应用程序配置之间交换数据存储和代码项目中的配置数据。 例如，你可以设置用于测试的一个应用配置存储区和另一个用于生产和复制它们通过的文件之间的应用程序设置，以便无需两次输入的数据。

本文提供导入和导出数据与应用程序配置的指南。

## <a name="import-data"></a>导入数据

导入提供的配置从现有源，而不是手动输入它们存储到应用程序配置中的数据。 导入功能可用于将数据迁移到应用程序配置存储区或从多个源聚合数据。 应用配置支持从文件导入 JSON、 YAML 或属性。

可以使用的数据导入[Azure 门户](https://aka.ms/azconfig/portal)或[Azure CLI](./scripts/cli-import.md)。 从 Azure 门户中，请执行以下步骤：

1. 浏览到您的应用程序配置存储区，然后单击**导入/导出**。

2. 在中**导入**选项卡上，选择**源服务**并**配置文件**。

3. 选择**语言**并**文件类型**。

4. 单击**文件夹**图标并浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

5. 选择**分隔符**并根据需要输入**前缀**要用于导入的密钥名称。

6. （可选） 选择**标签**。

7. 单击**应用**以完成导入。

    ![完成导入文件](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

导出将写入到另一个目标应用程序配置中存储的配置数据。 可用于导出函数，例如，将数据保存到在部署期间将使用应用程序代码嵌入的文件的应用程序配置存储区中。

可以使用的数据导出[Azure 门户](https://aka.ms/azconfig/portal)或[Azure CLI](./scripts/cli-export.md)。 从 Azure 门户中，请执行以下步骤：

1. 浏览到您的应用程序配置存储区，然后单击**导入/导出**。

2. 在中**导出**选项卡上，选择**服务为目标**并**配置文件**。

3. （可选） 输入**前缀**，然后选择**标签**和中的时间点要导出的密钥。

4. 选择**文件类型**并**分隔符**。

5. 单击**应用**，完成导出。

    ![已完成的导出文件](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 ASP.NET Web 应用](./quickstart-aspnet-core-app.md)  
