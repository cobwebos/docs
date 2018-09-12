---
title: 如何在 Azure VM 上将 Azure 资源的托管标识与 Azure SDK 配合使用
description: 将 Azure SDK 与具有 Azure 资源的托管标识的 Azure VM 配合使用的代码示例。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 36460257b5c170c600ef08eff07bb3f64a391cd8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343342"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>如何在 Azure VM 上将 Azure 资源的托管标识与 Azure SDK 配合使用 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
本文提供了 SDK 示例的列表，这些示例演示了如何使用其各自 Azure SDK 对 Azure 资源的托管标识的支持。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 本文中的所有示例代码/脚本假设客户端在已启用 Azure 资源的托管标识的 VM 上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 Azure 资源的托管标识的详细信息，请参阅[使用 Azure 门户在 VM 上配置 Azure 资源的托管标识](qs-configure-portal-windows-vm.md)，或有关在不同工具（使用 PowerShell、CLI、模板或 Azure SDK）中执行此操作的文章之一。 

## <a name="sdk-code-samples"></a>SDK 代码示例

| SDK 中 IsInRole 中的声明             | 代码示例 |
| --------------- | ----------- |
| .NET            | [使用 Azure 资源的托管标识从 Windows VM 部署 Azure 资源管理器模板](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [使用 Azure 资源的托管标识从 Linux VM 调用 Azure 服务](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [使用 Azure 资源的托管标识管理资源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [使用 Azure 资源的托管标识简单从 VM 内部进行身份验证](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [从启用了 Azure 资源的托管标识的 VM 中管理资源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure SDK](https://azure.microsoft.com/downloads/) 获取 Azure SDK 资源的完整列表，包括库下载、文档等。
- 若要启用 Azure VM 上的 Azure 资源的托管标识，请参阅[使用 Azure 门户在 VM 上配置 Azure 资源的托管标识](qs-configure-portal-windows-vm.md)。








