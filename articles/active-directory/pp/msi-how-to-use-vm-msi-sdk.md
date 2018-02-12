---
title: "如何在 VM 上通过 Azure SDK 使用用户分配的托管服务标识"
description: "在 VM 上将 Azure SDK 与用户分配的 MSI 配合使用的代码示例。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59d65e42c9b32bd0acd98645342833b4d57ad7a4
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>将 Azure SDK 与用户分配的托管服务标识 (MSI) 配合使用

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
本文提供了 SDK 示例的列表，这些示例演示了如何使用其各自 Azure SDK 对用户分配的 MSI 的支持。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - 本文中的所有示例代码/脚本假设客户端在已启用 MSI 的虚拟机上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure CLI 配置 VM 托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)，或有关在不同的工具中（使用 PowerShell、Azure 门户、模板或 Azure SDK）执行此操作的文章之一。 

## <a name="sdk-code-samples"></a>SDK 代码示例

| SDK 中 IsInRole 中的声明             | 代码示例 |
| --------------- | ----------- |
| Python          | [使用 MSI 从 VM 内部轻松进行身份验证](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [从已启用 MSI 的 VM 管理资源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure SDK](https://azure.microsoft.com/downloads/) 获取 Azure SDK 资源的完整列表，包括库下载、文档等。

使用以下评论部分提供反馈，帮助我们改进内容。








