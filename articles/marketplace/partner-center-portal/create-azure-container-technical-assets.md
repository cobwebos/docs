---
title: Azure 容器产品/服务的技术概念 - Microsoft 商业市场
description: 帮助你在 Azure 市场上配置容器产品/服务的技术资源和指南。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 87c915330fdc0d59d0d32c0747c7dc7647c342ba
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700977"
---
# <a name="create-an-azure-container-offer"></a>创建 Azure 容器产品/服务

本文提供技术资源和建议，以帮助你在 Azure 市场上创建容器产品/服务。

## <a name="before-you-begin"></a>开始之前

有关快速入门、教程和示例的信息，请参阅 [Azure 容器实例文档](https://docs.microsoft.com/azure/container-instances)。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。

除了解决方案领域以外，工程团队还应该了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
- [JSON](https://www.json.org/) 的实践知识。

## <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理容器映像：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

我们建议将以下工具添加到开发环境：

- [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

查看 [Azure 开发人员工具](https://azure.microsoft.com/)页中的可用工具。 如果使用的是 Visual Studio，请查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 中可用的工具。

## <a name="create-the-container-image"></a>创建容器映像

有关详细信息，请参阅以下教程：

- [教程：创建要部署到 Azure 容器实例的容器映像](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [教程：使用 Azure 容器注册表任务在云中生成并部署容器映像](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-steps"></a>后续步骤

- [创建容器产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)。
