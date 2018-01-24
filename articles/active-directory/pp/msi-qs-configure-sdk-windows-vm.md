---
title: "如何使用 Azure SDK 为 Azure VM 配置用户分配的 MSI"
description: "逐步介绍了如何使用 Azure SDK 为 Azure VM 配置用户分配的托管服务标识 (MSI)。"
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
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>使用 Azure SDK 为 VM 配置用户分配的托管服务标识 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文会介绍如何使用 Azure SDK 为 Azure VM 启用和删除用户分配的 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>支持 MSI 的 Azure SDK 

Azure 通过一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 支持多个编程平台。 其中几个平台已更新为支持 MSI，并提供相应的示例来演示用法。 由于添加了其他支持，此列表已更新：

| SDK 中 IsInRole 中的声明 | 示例 |
| --- | ------ | 
| Python | [创建启用了 MSI 的 VM](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [创建具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>后续步骤

- 若要了解如何在 Azure VM 上配置用户分配的 MSI，请参阅“为 Azure VM 配置 MSI”下的相关文章。

使用以下评论部分提供反馈，帮助我们改进内容。
