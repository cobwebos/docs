---
title: "如何将 Azure VM 托管服务标识与 Azure SDK 配合使用"
description: "将 Azure SDK 与 Azure VM MSI 配合使用的代码示例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 46fd75668999537dfbc9fd82c1f166e126b6f547
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>如何将 Azure VM 托管服务标识 (MSI) 与 Azure SDK 配合使用 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]本文提供了 SDK 示例的列表，这些示例演示了如何使用其各自 Azure SDK 对 MSI 的支持。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 本文中的所有示例代码/脚本假设客户端在已启用 MSI 的虚拟机上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)，或有关在不同的工具中（使用 PowerShell、CLI、模板或 Azure SDK）执行此操作的文章之一。 

## <a name="sdk-code-samples"></a>SDK 代码示例

| SDK 中 IsInRole 中的声明             | 代码示例 |
| --------------- | ----------- |
| .NET            | [使用托管服务标识从 Windows VM 部署 Azure 资源管理器模板](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [使用托管服务标识从 Linux VM 调用 Azure 服务](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [使用托管服务标识管理资源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [使用 MSI 从 VM 内部轻松进行身份验证](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [从已启用 MSI 的 VM 管理资源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>相关内容

- 请参阅 [Azure SDK](https://azure.microsoft.com/downloads/) 获取 Azure SDK 资源的完整列表，包括库下载、文档等。
- 若要在 Azure VM 上启用 MSI，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。








