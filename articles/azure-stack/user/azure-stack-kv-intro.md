---
title: Azure 堆栈密钥保管库简介 |Microsoft 文档
description: 了解如何 Azure 堆栈密钥保管库管理密钥和机密
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: mabrigg
ms.openlocfilehash: a50a03e70ccf014a8a9d33e0f177febed560853f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636720"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>介绍 Azure 堆栈中的密钥保管库

## <a name="prerequisites"></a>必备组件 

* 您必须订阅服务关联，它包含 Azure 密钥保管库服务。  
* [PowerShell 配置为用于 Azure 堆栈](azure-stack-powershell-configure-user.md)。
 
## <a name="key-vault-basics"></a>密钥保管库的基础知识
Azure 堆栈中的密钥保管库有助于保护加密密钥和机密与云应用程序和服务使用。 通过使用密钥保管库，你可以加密密钥和机密，例如：
   * 身份验证密钥 
   * 存储帐户密钥
   * 数据加密密钥
   * .pfx 文件
   * 密码

Key Vault 简化了密钥管理过程，可让我们控制用于访问和加密数据的密钥。 开发人员可以在几分钟内创建用于开发和测试的密钥，然后无缝地将其迁移到生产密钥。 安全管理员可以根据需要授予（和吊销）密钥权限。

具有 Azure 堆栈订阅的任何人可以创建和使用密钥保管库。 尽管密钥保管库受益开发人员和安全管理员，则管理其他 Azure 堆栈服务的组织的操作员可以实现和管理它。 例如，可以使用 Azure 堆栈的订阅，登录运算符 Azure 堆栈创建组织用来存储密钥，并将这些操作的任务负责的保管库：

* 创建或导入密钥或机密。
* 撤消或删除密钥或机密。
* 授权用户或应用程序用于访问密钥保管库，使它们可以然后管理或使用其密钥和机密。
* 配置密钥用法 （例如，签名或加密）。

然后，运算符可以提供开发人员使用统一资源标识符 (Uri) 从其应用程序调用。 运算符还可以为安全管理员提供密钥用法日志记录信息。

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅密钥保管库开发人员指南。

## <a name="scenarios"></a>方案
下列方案说明了如何密钥保管库可帮助满足的需求的开发人员和安全管理员。

### <a name="developer-for-an-azure-stack-application"></a>Azure 堆栈应用程序的的开发人员
**问题：** 我想要为使用密钥进行签名和加密的 Azure 堆栈编写应用程序。 我想要从我的应用程序，外部，以便解决方案地理上分散的应用程序适用于这些键。

**语句：** 密钥将存储在保管库中并在需要时由 URI 调用。

### <a name="developer-for-software-as-a-service-saas"></a>软件即服务 (SaaS) 开发人员
**问题：** 对于我的客户的密钥和机密，我不想承担任何实际或潜在责任。 我希望客户拥有并管理其键，以便我可以专注于执行干什么最，也就提供核心软件功能。

**语句：** 客户可以将其自己的密钥导入 Azure 堆栈，然后管理它们。 

### <a name="chief-security-officer-cso"></a>首席安全官 (CSO)
**问题：** 我想要确保我的组织掌控密钥生命周期，并可监视密钥用法。

**语句：** 密钥保管库进行设计，以便 Microsoft 不会看到或提取你的密钥。 当应用程序需要使用客户密钥执行加密操作时，密钥保管库将使用代表应用程序密钥。 应用程序将不看到客户的密钥。 尽管我们使用多个 Azure 堆栈服务和资源，你可以从 Azure 堆栈中的单个位置管理密钥。 保管库提供单个界面，无论多少保管库中有 Azure 堆栈，哪些区域它们支持和哪些应用程序使用它们。

## <a name="next-steps"></a>后续步骤

* [使用门户管理 Azure 堆栈中的密钥保管库](azure-stack-kv-manage-portal.md)  
* [使用 PowerShell 管理 Azure 堆栈中的密钥保管库](azure-stack-kv-manage-powershell.md)

