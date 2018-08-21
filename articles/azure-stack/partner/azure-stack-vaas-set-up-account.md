---
title: 设置你的 Azure Stack 验证作为服务帐户 |Microsoft Docs
description: 了解如何设置你的验证作为服务帐户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: f6c78a3e79ac88194e7e34ad8be7a941ee715d39
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235278"
---
# <a name="set-up-your-validation-as-a-service-account"></a>设置你的验证作为服务帐户

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

作为一项服务 (VaaS) 验证是一项 Azure 服务，可供 Microsoft Azure Stack 合作伙伴共同工程与 Microsoft 签订协议来设计、 开发、 验证、 销售、 部署和支持 Azure Stack 解决方案在市场中。

了解如何使您的系统可供服务的验证。 设置 Azure Active Directory 实例，并为准备好使用 VaaS 执行其他必需的任务。 

任务包括：

- 创建 Azure 存储 blob 来存储日志
- 部署本地代理
- 下载 Azure Stack 实例要测试的测试映像虚拟机

## <a name="create-an-azure-active-directory-tenant-id"></a>创建 Azure Active Directory 租户 ID

1. 创建一个可在 Azure Active Directory 租户[Azure 门户](https://portal.azure.com)或使用现有租户。

    建议创建专用于租户与 VaaS 具有一个描述性名称，如， ContosoVaaS@onmicrosoft.com。 合作伙伴将使用租户的基于角色的访问控制 (RBAC) 功能来管理合作伙伴组织中的哪些人员可以使用 VaaS。  
    
    详细信息，请参阅[管理 Azure AD 目录](https://docs.microsoft.com/azure/active-directory/active-directory-administer)。

    > [!Note]  
    > 有关创建新的 Azure Active Directory 租户的详细信息，请参阅[开始使用 Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)。

2. 添加将负责使用服务租户将你的组织的成员。 将分配每个租户中的用户的以下角色之一来控制对 VaaS 其访问级别：

    | 角色名称 | 说明 |
    |---------------------|------------------------------------------|
    | 所有者 | 具有完全访问权限的所有资源。 |
    | 读取器 | 可以查看所有资源，但无法编辑。 |
    | 测试参与者 | 可以管理测试资源。 |
    | 目录参与者 | 可以管理解决方案发布资源。 |

## <a name="set-up-your-tenant"></a>设置租户

设置在租户**Azure Stack 验证服务**应用程序。 

1. 有关租户的以下信息发送给 Microsoft 在vaashelp@microsoft.com。

    | 数据 | 说明 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 组织名称 | 官方组织名称。 |
    | Azure AD 租户目录名称 | 要注册的 azure AD 租户目录名称。 |
    | Azure AD 租户目录 Id | Azure AD 租户目录与目录关联的 GUID。<br> 了解如何查找 Azure AD 租户目录 ID 可以找到，请参阅"[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。" |

    

2. Azure Stack 团队提供了你的租户可以使用 VaaS 门户确认。

3. 使用租户的全局管理员凭据登录到[VaaS 门户](https://azurestackvalidation.com/
)。 选择**我的帐户**。

    ![登录到 VaaS 门户](media/vaas_portalsignin.png)

3. 在站点系统将提示您为 VaaS 授予访问权限。 接受条款以继续。

## <a name="assign-user-roles"></a>为用户分配角色

若要将用户角色分配：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**所有服务** > **Azure Active Directory**中**标识**组。
3. 选择**企业应用程序** > **Azure Stack 验证服务**应用程序。
4. 选择“用户和组”。 **Azure Stack 验证服务的用户和组**边栏选项卡列出有权使用的应用程序的用户。
5. 选择 **+ 添加用户**添加作业。

## <a name="create-an-azure-storage-blob-to-store-logs"></a>创建 Azure 存储 blob 来存储日志

VaaS 创建诊断日志时运行验证测试。 需要一个位置的 Azure blob 服务 SAS URL 来存储你的日志。 应用商店 OEM 自定义包，可能也可用的存储帐户。

以下步骤演示了如何设置和生成 Azure 存储帐户的存储即服务 (SAS) URI 以及在门户中启动测试时 VaaS 门户中指定的存储帐户的位置。

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 若要创建存储帐户，请按照说明进行操作，[创建存储帐户](https://docs.microsoft.com/azure/storage/storage-create-storage-account#create-a-storage-account)。

2. 选择存储帐户的类型时，选择**Blob 存储**帐户类型。

### <a name="generate-a-sas-url-for-the-storage-account"></a>生成的存储帐户的 SAS URL

1. 导航到上面创建的存储帐户。

2. 在边栏选项卡上**设置**，选择**共享访问签名**。

3. 仅检查**Blob**从**允许的服务选项**（取消选中其余）。

4. 检查**服务**，* * 容器，并**对象**从**允许的资源类型**。

5. 检查**读**，**编写**，**列表**，**添加**，**创建**从**允许权限** （取消选中其余）。

6. 设置**开始时间**为当前时间，并**结束时间**到三个月内从当前时间。

7. 选择**生成 SAS 和连接字符串**并保存**Blob 服务 SAS URL**字符串。

> [!Note]  
> SAS URL 将在生成 URL 时设置的结束时间到期。 请确保共享与产品团队进行调试之前, 的 URL 充分有效或计划测试时，URL 是对超过 30 天有效。

## <a name="next-steps"></a>后续步骤

- 使用 VaaS 本地代理来检查您的硬件。 有关说明，请参阅[部署本地代理和测试虚拟机](azure-stack-vaas-test-vm.md)。
- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。