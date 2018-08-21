---
title: 工作流通用参数中作为服务的 Azure Stack 验证 |Microsoft Docs
description: 工作流通用参数作为服务的 Azure Stack 验证
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235214"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>工作流通用参数作为服务的 Azure Stack 验证

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

常见参数包括值，例如环境变量和用户凭据所需的验证作为一项服务 (VaaS) 中的所有测试。 定义这些值在工作流级别。 创建或修改工作流时保存的值。 在计划时，工作流加载测试的值。 

## <a name="environment-parameters"></a>环境的参数

环境参数描述待测试的 Azure Stack 环境。 必须提供这些值通过生成并上传戳配置文件 `&lt;link&gt;. [How to get the stamp info link].`

| 参数名称 | 需要 | Type | 说明 |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack 内部版本 | 需要 |  | Azure Stack 部署 (例如，1.0.170330.9) 的生成号 |
| OEM 版本 | 是 |  | 在 Azure Stack 部署过程中使用的 OEM 包的版本号。 |
| OEM 签名 | 是 |  | 在 Azure Stack 部署过程中使用的 OEM 包的签名。 |
| AAD 租户 ID | 需要 |  | Azure Active Directory 租户 Azure Stack 部署过程中指定的 GUID。|
| 区域 | 需要 |  | Azure Stack 部署区域。 |
| 租户资源管理器终结点 | 需要 |  | 租户 Azure 资源管理器操作的终结点 (例如， https://management。<ExternalFqdn>) |
| 管理资源管理器终结点 | 是 |  | 租户 Azure 资源管理器操作终结点 (例如， https://adminmanagement。<ExternalFqdn>) |
| 外部 FQDN | 是 |  | 外部完全限定的域名作为后缀用于终结点。 （例如，local.azurestack.external 或 redmond.contoso.com）。 |
| 节点数 | 是 |  | 在部署上的节点数。 |

## <a name="test-parameters"></a>测试参数

常见的测试参数包括的敏感信息不能存储在配置文件，必须手动提供。

| 参数名称 | 需要 | Type | 说明 |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 租户用户名 | 需要 |  | Azure Active Directory 租户管理员或者已预配或只需将用户预配服务管理员在 AAD 目录中。 有关预配租户帐户的详细信息，请参阅[开始使用 Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)。 此值用于测试执行租户操作，例如部署模板来预配资源 （Vm、 存储帐户等） 并执行工作负荷。 此值用于测试执行租户操作，例如部署模板来预配资源 （Vm、 存储帐户等） 并执行工作负荷。 |
| 租户密码 | 需要 |  | 租户用户的密码。 |
| 服务管理员用户名 | 所需的： 解决方案验证，在包验证<br>不是必需的： 测试通过 |  | 在 Azure Stack 部署过程中指定的 AAD 目录租户的 azure Active Directory 管理员。 |
| 服务管理员密码 | 所需的： 解决方案验证，在包验证<br>不是必需的： 测试通过 |  | 服务管理员用户密码。 |
| 云管理员用户名 | 需要 |  | Azure Stack 域管理员帐户 (例如，contoso\cloudadmin)。 搜索为用户角色 ="CloudAdmin"配置文件中的，并选择配置文件中的用户名标记中的值。 |
| 云管理员密码 | 需要 |  | 云管理员用户密码。 |
| 诊断连接字符串 | 需要 |  | 指向 Azure 存储帐户的诊断日志将复制到测试执行期间的 SAS URI。 说明用于生成 SAS URI[设置 blob 存储帐户](azure-stack-vaas-set-up-account.md)。 |


## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
