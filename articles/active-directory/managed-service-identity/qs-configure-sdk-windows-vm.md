---
title: 如何使用 Azure SDK 配置启用了 MSI 的 Azure VM
description: 分步说明如何通过 Azure SDK 在 Azure VM 上配置和使用托管服务标识 (MSI)。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 781f332b2892d9af536bf9a6f81642842285927b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>使用 Azure SDK 配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory (AD) 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure SDK 为 Azure VM 启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>支持 MSI 的 Azure SDK 

Azure 通过一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 支持多个编程平台。 其中几个平台已更新为支持 MSI，并提供相应的示例来演示用法。 由于添加了其他支持，此列表已更新：

| SDK 中 IsInRole 中的声明 | 示例 |
| --- | ------ | 
| .NET   | [从已启用 MSI 的 VM 管理资源](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [从启用了 MSI 的 VM 管理存储](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [创建启用了 MSI 的 VM](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [创建启用了 MSI 的 VM](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [创建具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>后续步骤

- 请参阅“为 Azure VM 配置 MSI”下的相关文章，了解如何还可以使用 Azure 门户、PowerShell、CLI 和资源模板完成此操作。

使用以下评论部分提供反馈，帮助我们改进内容。
