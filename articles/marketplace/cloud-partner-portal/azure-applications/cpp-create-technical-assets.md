---
title: 创建 Azure 应用程序技术资产 |Azure Marketplace
description: 为 Azure 应用程序产品/服务创建技术资产。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942985"
---
# <a name="prepare-your-azure-application-technical-assets"></a>准备 Azure 应用程序技术资产

本文介绍了用于为 Azure 应用程序产品/服务准备技术资产的资源。

## <a name="before-you-begin"></a>开始之前

观看以下视频（[针对 Azure 市场构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)），该视频首先概述了如何创建 Azure 资源管理器模板来定义 Azure 应用程序解决方案，然后概述了之后如何将应用套餐发布到 Azure 市场。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


查看以下 Azure 应用程序文档，其中提供了快速入门、教程和示例。

- [了解 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- 快速入门：

  - [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
  - [发布应用程序定义](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [部署服务目录应用](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- 教程：

  - [创建定义文件](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [发布市场应用程序](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - 示例：

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [托管应用程序解决方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。

工程团队应该了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
- [JSON](https://www.json.org/) 的实践知识

## <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理 Azure 应用程序：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

我们建议将以下工具添加到开发环境：

- [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：

  - 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

我们还建议在 [Azure 开发人员工具](https://azure.microsoft.com/tools/)页中查看可用的工具；如果使用的是 Visual Studio，请在 [Visual Studio 市场](https://marketplace.visualstudio.com/)中查看。

## <a name="next-steps"></a>后续步骤

[创建 Azure 应用程序套餐](./cpp-create-offer.md)

