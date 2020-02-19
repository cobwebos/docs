---
title: 创建 Cordova 应用
description: 遵循本教程开始使用 Azure 移动应用后端进行 Apache Cordova 开发
keywords: cordova,javascript,移动,客户端
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 99fb4a4b07ecbd4a85abbc62ec52a0f5960654c5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461566"
---
# <a name="create-an-apache-cordova-app"></a>创建 Apache Cordova 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概述
本教程说明如何使用 Azure 移动应用后端向 Apache Cordova 移动应用添加基于云的后端服务。  将创建一个新的移动应用后端以及一个简单的*待办事项列表* Apache Cordova 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关使用 Azure 应用服务中的移动应用功能的所有其他 Apache Cordova 教程。

## <a name="prerequisites"></a>必备条件
若要完成本教程，需要具备以下先决条件：

* 装有 [Visual Studio Community 2017] 或更高版本的电脑。
* [用于 Apache Cordova 的 Visual Studio 工具]
* [有效的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。

也可以绕过 Visual Studio，直接使用 Apache Cordova 命令行。  在 Mac 计算机上学习本教程时，使用命令行相当有效。  本教程不介绍如何使用命令行编译 Apache Cordova 客户端应用程序。

## <a name="create-an-azure-mobile-app-backend"></a>创建 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>下载并运行 Apache Cordova 应用
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[用于 Apache Cordova 的 Visual Studio 工具]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
