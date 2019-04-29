---
title: 创建 Azure 容器映像技术资产 | Microsoft Docs
description: 为 Azure 容器创建技术资产。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 5a7531be73a872d9c088a0bf02a8686f947c220a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472934"
---
# <a name="prepare-your-container-technical-assets"></a>准备容器技术资产

本文介绍了在 Azure 市场中配置容器产品/服务的步骤和要求。

## <a name="before-you-begin"></a>开始之前

查看 [Azure 容器实例](https://docs.microsoft.com/azure/container-instances)文档，其中提供了快速入门、教程和示例。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。
 
除了解决方案领域以外，工程团队还应该了解以下 Microsoft 技术：

-   基本了解 [Azure 服务](https://azure.microsoft.com/services/) 
-   如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
-   [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
-   [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
-   [JSON](https://www.json.org/) 的实践知识

## <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理容器映像：

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

此外，我们建议将以下工具添加到开发环境：

-   [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

我们还建议在 [Azure 开发人员工具](https://azure.microsoft.com/tools/)页中查看可用的工具；如果使用的是 Visual Studio，请在 [Visual Studio 市场](https://marketplace.visualstudio.com/)中查看。

## <a name="create-the-container-image"></a>创建容器映像

- 为容器虚拟机 (VM) 创建和配置虚拟硬盘 (VHD)。 此 VHD 包含容器的操作系统（Windows、Linux 或 Ubuntu）。 可能需要更多数据磁盘。
- 配置 VM OS、VM 大小、要打开的端口，以及任何附加的数据磁盘。
- 安装你的产品/服务所需的应用程序和其他软件。 例如：数据库软件、第三方软件或自定义应用程序。

## <a name="next-steps"></a>后续步骤

[创建容器产品/服务](./cpp-create-offer.md)
