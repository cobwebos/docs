---
title: 教程-为作为服务的验证设置的资源 |Microsoft Docs
description: 在本教程中，了解如何设置验证为服务资源。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0830deaaa1161880706c39aee741af06a41b4b6f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426324"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>教程： 为作为服务的验证设置的资源

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

您需要创建一个解决方案。 验证服务 (VaaS) 解决方案作为表示与特定硬件清单的材料的 Azure Stack 解决方案。 该解决方案将用于检查你的硬件可以支持运行的 Azure Stack。 按照本教程，若要准备好将服务用于在你的解决方案。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备好供 VaaS 设置 Azure AD (Azure AD) 实例。
> * 创建存储帐户。

## <a name="configure-an-azure-ad-tenant"></a>配置 Azure AD 租户

Azure AD 租户是进行身份验证和注册 VaaS 所必需的。 合作伙伴将使用租户的基于角色的访问控制 (RBAC) 功能来管理合作伙伴组织中的哪些人员可以使用 VaaS。

注册你组织的 Azure AD 租户目录 （而不是用于 Azure Stack 的 Azure AD 租户目录），并建立用于管理中它的用户帐户的策略。 有关详细信息，请参阅[管理 Azure AD 目录](https://docs.microsoft.com/azure/active-directory/active-directory-administer)。

### <a name="create-a-tenant"></a>创建租户

创建租户专门用于与 VaaS 具有说明性的名称，例如， `ContosoVaaS@onmicrosoft.com`。

1. 创建一个可在 Azure AD 租户[Azure 门户](https://portal.azure.com)，或使用现有租户。 <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. 将你的组织的成员添加到租户。 这些用户将负责使用服务来查看或计划测试。 一旦完成注册后，您将定义用户的访问级别。
 
    为你的租户在 VaaS 中执行操作，通过将分配以下角色之一的用户授权：

    | 角色名称 | 说明 |
    |---------------------|------------------------------------------|
    | 所有者 | 具有对所有资源的完全访问权限。 |
    | 读取器 | 可以查看所有资源，但不是创建或管理。 |
    | 测试参与者 | 可以创建和管理测试资源。 |

    若要在中分配角色**Azure Stack 验证服务**应用程序：

    1. 登录到 [Azure 门户](https://portal.azure.com)。
    2. 选择**所有服务** > **Azure Active Directory**下**标识**部分。
    3. 选择“企业应用程序” > “Azure Stack 验证服务”应用程序。
    4. 选择“用户和组”。 “Azure Stack 验证服务 - 用户和组”边栏选项卡会列出有权使用应用程序的用户。
    5. 选择 **+ 添加用户**若要从你的租户添加用户并分配角色。
   
    如果你想要隔离 VaaS 资源和组织内不同的组的操作，可以创建多个 Azure AD 租户目录。

### <a name="register-your-tenant"></a>注册你的租户

此过程会授权你使用的租户**Azure Stack 验证服务**Azure AD 应用程序。

1. 将有关租户的以下信息发送到在 Microsoft [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)。

    | 数据 | 说明 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 组织名称 | 官方组织名称。 |
    | Azure AD 租户目录名称 | 正在注册的 Azure AD 租户目录名称。 |
    | Azure AD 租户目录 ID | Azure AD 租户目录与目录关联的 GUID。 有关如何查找你的 Azure AD 租户目录 ID 的信息，请参阅[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。 |

2. 等待来自 Azure Stack 验证团队来检查你的租户可以使用 VaaS 门户确认。

### <a name="consent-to-the-vaas-application"></a>同意 VaaS 应用程序

作为 Azure AD 管理员，请为 VaaS Azure AD 应用程序指定代表你的租户所需的权限：

1. 使用租户的全局管理员凭据登录到 [VaaS 门户](https://azurestackvalidation.com/)。 

2. 选择“我的帐户”。

3 接受条款才能继续操作时提示你授予 VaaS 列出 Azure AD 的权限。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

在测试执行期间 VaaS 输出到 Azure 存储帐户的诊断日志。 除了测试日志的存储帐户可能还用于上传 OEM 扩展包在包验证工作流。

在 Azure 公有云，不能在 Azure Stack 环境中托管的 Azure 存储帐户。

1. 在 Azure 门户中，选择**所有服务** > **存储** > **存储帐户**。 上**存储帐户**边栏选项卡，选择**添加**。

2. 选择要在其中创建存储帐户的订阅。

3. 下**资源组**，选择**新建**。 输入新的资源组的名称。

4. 输入存储帐户的名称。 你选择的名称必须是：
    - 在 Azure 内唯一
    - 介于 3 到 24 个字符之间
    - 只能包含数字和小写字母

5. 选择**美国西部**为存储帐户的区域。

    若要确保网络费用不会对存储日志，可以配置 Azure 存储帐户为仅使用**美国西部**区域。 数据复制和热存储层功能则不需要此数据。 启用这两个功能将显著增加成本。

6. 将设置保留为默认值除外**帐户类型**:

    - **部署模型**字段设置为**资源管理器**默认情况下。
    - “性能”字段默认设置为“标准”。
    - 选择**帐户类型**字段作为**Blob 存储**。
    - **复制字段**设置为**本地冗余存储 (LRS)** 默认情况下。
    - “访问层”默认设置为“热”。

7. 单击“查看+创建”可查看存储帐户设置并创建帐户。

## <a name="next-steps"></a>后续步骤

如果你的环境不允许的入站连接，并遵照本教程部署您的硬件上运行测试的本地代理。

> [!div class="nextstepaction"]
> [部署本地代理](azure-stack-vaas-local-agent.md)
