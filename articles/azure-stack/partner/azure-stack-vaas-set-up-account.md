---
title: 设置你的 Azure Stack 验证作为服务帐户 |Microsoft Docs
description: 了解如何设置验证即服务帐户。
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
ms.openlocfilehash: b94d37d528739247cb4f94a17dadf3876c4db6bd
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158502"
---
# <a name="set-up-your-validation-as-a-service-account"></a>设置验证即服务帐户

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

作为一项服务 (VaaS) 验证是一项 Azure 服务，可供 Microsoft Azure Stack 合作伙伴共同工程与 Microsoft 签订协议来设计、 开发、 验证、 销售、 部署和支持 Azure Stack 解决方案在市场中。

了解如何针对验证即服务来准备系统。 设置 Azure Active Directory 实例并执行其他必要任务，做好使用 VaaS 的准备。 

任务包括：

- 创建 Azure 存储 Blob 来存储日志
- 部署本地代理
- 在要测试的 Azure Stack 实例上下载测试映像虚拟机

## <a name="create-an-azure-active-directory-tenant-id"></a>创建 Azure Active Directory 租户 ID

1. 在 [Azure 门户](https://portal.azure.com)中创建 Azure Active Directory 租户，或者使用现有的租户。

    建议使用描述性名称创建一个专用于 VaaS 的租户，例如 ContosoVaaS@onmicrosoft.com。 租户的基于角色的访问控制 (RBAC) 功能可供合作伙伴用于管理在合作伙伴组织中能够使用 VaaS 的人员。  
    
    有关详细信息，请参阅[管理 Azure AD 目录](https://docs.microsoft.com/azure/active-directory/active-directory-administer)。

    > [!Note]  
    > 有关创建新的 Azure Active Directory 租户的详细信息，请参阅[开始使用 Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)。

2. 添加组织中负责对租户使用此服务的成员。 为租户中的每个用户分配下述角色之一，控制其对 VaaS 的访问级别：

    | 角色名称 | 说明 |
    |---------------------|------------------------------------------|
    | 所有者 | 具有对所有资源的完全访问权限。 |
    | 读取器 | 可以查看所有资源，但不能进行编辑。 |
    | 测试参与者 | 可以管理测试资源。 |
    | 目录参与者 | 可以管理解决方案发布资源。 |

## <a name="set-up-your-tenant"></a>设置租户

在 **Azure Stack 验证服务**应用程序中设置租户。 

1. 有关租户的以下信息发送给 Microsoft 在vaashelp@microsoft.com。

    | 数据 | 说明 |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | 组织名称 | 官方组织名称。 |
    | Azure AD 租户目录名称 | 注册的 Azure AD 租户目录名称。 |
    | Azure AD 租户目录 ID | 与目录关联的 Azure AD 租户目录 GUID。<br> 若要了解如何查找 Azure AD 租户目录 ID，请参阅“[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)”。 |

    

2. Azure Stack 通过你的租户可以使用 VaaS 门户的确认。

3. 使用租户的全局管理员凭据登录到 [VaaS 门户](https://azurestackvalidation.com/
)。 选择“我的帐户”。

    ![登录到 VaaS 门户](media/vaas_portalsignin.png)

3. 此站点会提示你授予对 VaaS 的访问权限。 接受条款即可继续。

## <a name="assign-user-roles"></a>分配用户角色

若要分配用户角色，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“标识”组中选择“所有服务” > “Azure Active Directory”。
3. 选择“企业应用程序” > “Azure Stack 验证服务”应用程序。
4. 选择“用户和组”。 “Azure Stack 验证服务 - 用户和组”边栏选项卡会列出有权使用应用程序的用户。
5. 选择“+ 添加用户”即可添加分配。

## <a name="create-an-azure-storage-blob-to-store-logs"></a>创建 Azure 存储 Blob 来存储日志

VaaS 在运行验证测试时会创建诊断日志。 需要位置和 Azure Blob 服务 SAS URL 来存储日志。 存储帐户也可用来存储 OEM 自定义包。

以下步骤演示了如何为 Azure 存储帐户设置和生成存储即服务 (SAS) URI，以及在 VaaS 门户中开始测试时，在何处指定存储帐户。

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 若要创建存储帐户，请按[创建存储帐户](../../storage/common/storage-quickstart-create-account.md)中的说明操作。

2. 选择存储帐户的类型时，请选择“Blob 存储”帐户类型。

### <a name="generate-a-sas-url-for-the-storage-account"></a>生成存储帐户的 SAS URL

1. 导航到上面创建的存储帐户。

2. 在边栏选项卡的“设置”下，选择“共享访问签名”。

3. 在“允许的服务选项”中，仅选择“Blob”（取消选中其余选项）。

4. 在“允许的资源类型”中，勾选“服务”、“容器”和“对象”。**

5. 在“允许的权限”中，勾选“读取”、“写入”、“列表”、“添加”、“创建”（取消选中其余选项）。

6. 将“开始时间”设置为当前时间，将“结束时间”设置为距当前时间三个月的未来时间。

7. 选择“生成 SAS 和连接字符串”，然后保存“Blob 服务 SAS URL”字符串。

> [!Note]  
> SAS URL 在结束时间（在生成 URL 时设置）过期。 请确保 URL 的有效时间足够长，然后再将其与产品团队共享，方便进行调试，或者确保在计划测试时，该 URL 的有效时间为 30 天以上。

## <a name="next-steps"></a>后续步骤

- 使用 VaaS 本地代理来检查硬件。 有关说明，请参阅[部署本地代理并测试虚拟机](azure-stack-vaas-test-vm.md)。
- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。