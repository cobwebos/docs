---
title: 创建 Azure VM 技术资产
description: 本文介绍了在 Azure 应用商店上配置容器产品提供的步骤和要求。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730628"
---
# <a name="create-an-azure-vm-technical-asset"></a>创建 Azure VM 技术资产

> [!IMPORTANT]
> 我们将 Azure 容器产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照云合作伙伴门户[准备容器技术资产](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets)以管理优惠的说明进行操作。

本文介绍了在 Azure 应用商店上配置容器产品提供的步骤和要求。

## <a name="before-you-begin"></a>开始之前

有关快速入门、教程和示例，请参阅[Azure 容器实例](https://docs.microsoft.com/azure/container-instances)。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、构建和测试这些资产需要时间，需要对 Azure 平台和用于构建产品/服务的技术进行技术知识。

除了解决方案领域之外，您的工程团队还应了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
- [JSON](https://www.json.org/)的工作知识 。

## <a name="suggested-tools"></a>建议的工具

选择以下脚本环境的一个或两个，以帮助管理容器映像：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

我们建议将这些工具添加到开发环境中：

- [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 扩展：[预化 JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

查看[Azure 开发人员工具](https://azure.microsoft.com/)页面上的可用工具。 如果您使用的是可视化工作室，请查看[可视化工作室应用商店](https://marketplace.visualstudio.com/)中可用的工具。

## <a name="create-the-container-image"></a>创建容器映像

有关详细信息，请参阅以下教程：

- [教程：创建容器映像以部署到 Azure 容器实例](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [教程：使用 Azure 容器注册表任务在云中构建和部署容器映像](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)。

## <a name="next-step"></a>后续步骤

- [创建您的容器产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)。
