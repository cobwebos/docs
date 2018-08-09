---
title: Azure Databricks：常见问题和帮助 | Microsoft Docs
description: 获取有关 Azure Databricks 的常见问题的答案和故障诊断信息。
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c3ba235c60480c38a21ee3264c54b4a4dcdea340
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434595"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>有关 Azure Databricks 的常见问题解答

本文列出用户可能会遇到的与 Azure Databricks 相关的热门查询。 以及使用 Databricks 时可能会遇到的一些常见问题。 有关详细信息，请参阅[什么是 Azure Databricks](what-is-azure-databricks.md)？ 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>能否使用我自己的密钥进行本地加密？ 
当前版本不支持使用 Azure Key Vault 中你自己的密钥。 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>可以借助 Databricks 使用 Azure 虚拟网络吗？
Databricks 预配过程中会创建新的虚拟网络。 不能在此版本中使用自己的 Azure 虚拟网络。

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>如何使用笔记本访问 Azure Data Lake Store？ 

执行以下步骤:
1. 在 Azure Active Directory (Azure AD) 中预配服务主体并记录其密钥。
1. 在 Data Lake Store 中分配服务主体的必需权限。
1. 若要访问 Data Lake Store 中的文件，请使用 Notebook 中的服务主体凭据。

有关详细信息，请参阅[配合使用 Data Lake Store 和 Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)。

## <a name="fix-common-problems"></a>解决常见问题

以下是使用 Databricks 时可能会遇到的一些问题。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>问题：该订阅未注册为使用命名空间“Microsoft.Databricks”

#### <a name="error-message"></a>错误消息

“该订阅未注册为使用命名空间‘Microsoft.Databricks’。 有关如何注册订阅，请参阅 https://aka.ms/rps-not-found。 （代码：MissingSubscriptionRegistration）”

#### <a name="solution"></a>解决方案

1. 转到 [Azure 门户](https://portal.azure.com)。
1. 依次选择“订阅”、正在使用的订阅，然后单击“资源提供程序”。 
1. 在资源提供程序列表中，针对“Microsoft.Databricks”选择“注册”。 必须具有订阅的参与者或所有者角色才能注册资源提供程序。


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>问题：在 Azure 门户中，你的帐户 {email} 没有 Databricks 工作区资源的“所有者”或“参与者”角色

#### <a name="error-message"></a>错误消息

“在 Azure 门户中，你的帐户 {email} 没有 Databricks 工作区资源的‘所有者’或‘参与者’角色。 如果你是租户中的来宾用户，也可能发生此错误。 请让管理员授予你访问权限，或直接在 Databricks 工作区中将你添加为用户。” 

#### <a name="solution"></a>解决方案

以下是此问题的一些解决方法：

* 若要初始化租户，必须以租户的常规用户（而非来宾用户）身份登录。 还必须具有 Databricks 工作区资源的“参与者”角色。 可以在 Azure 门户的 Databricks 工作区中，通过“访问控制(IAM)”选项卡授予用户访问权限。

* 如果电子邮件域名在 Azure AD 中被分配给多个目录，也可能会发生此错误。 若要解决此问题，可在包含订阅和 Databricks 工作区的目录中创建新用户。

    a. 在 Azure 门户中，转到 Azure AD。 依次选择“用户和组” > “添加用户”。

    b. 使用 `@<tenant_name>.onmicrosoft.com` 电子邮件而非 `@<your_domain>` 电子邮件添加用户。 可在 Azure 门户中 Azure AD 下的“自定义域”中找到此选项。
    
    c. 授予新用户 Databricks 工作区资源的“参与者”角色。
    
    d. 使用新用户登录到 Azure 门户，并找到 Databricks 工作区。
    
    e. 以此用户的身份启动 Databricks 工作区。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>问题：你的帐户 {电子邮件} 未在 Databricks 中注册 

#### <a name="solution"></a>解决方案

如果你未创建工作区，但要添加为用户，请联系创建工作区的人员。 让他通过 Azure Databricks 管理员控制台添加。 有关说明，请参阅 [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)（添加和管理用户）。 如果已创建该工作区但仍出现此错误，请尝试再次在 Azure 门户中单击“初始化工作区”。

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>问题：设置群集时，云提供程序启动失败 (PublicIPCountLimitReached)

#### <a name="error-message"></a>错误消息

“云提供程序启动故障: 在设置群集时遇到云提供程序错误。 有关详细信息，请参阅“Databricks 指南”。 Azure 错误代码：PublicIPCountLimitReached。 Azure 错误消息: 不能在此区域为该订阅创建超过 60 个公共 IP 地址。”

#### <a name="solution"></a>解决方案

Databricks 群集为每个节点使用一个公共 IP 地址。 如果订阅已使用其所有的公共 IP，则应[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。 选择“配额”作为“问题类型”、选择“网络: ARM”作为“配额类型”。 在“详细信息”中，请求增加公共 IP 地址配额。 例如，如果限制当前为 60，但希望创建具有 100 个节点的群集，则请求将限制增加至 160。

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>问题：设置群集时，第二种类型的云提供程序启动失败 (MissingSubscriptionRegistration)

#### <a name="error-message"></a>错误消息

“云提供程序启动故障: 在设置群集时遇到云提供程序错误。 有关详细信息，请参阅“Databricks 指南”。
Azure 错误代码：MissingSubscriptionRegistration；Azure 错误消息：订阅未注册为使用命名空间“Microsoft.Databricks”。 有关如何注册订阅，请参阅 https://aka.ms/rps-not-found。

#### <a name="solution"></a>解决方案

1. 转到 [Azure 门户](https://portal.azure.com)。
1. 依次选择“订阅”、正在使用的订阅，然后单击“资源提供程序”。 
1. 在资源提供程序列表中，针对“Microsoft.Compute”选择“注册”。 必须具有订阅的参与者或所有者角色才能注册资源提供程序。

有关详细说明，请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)。

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>问题：Azure Databricks 需要只有管理员可以授予的访问组织中资源的权限。

#### <a name="background"></a>背景

Azure Databricks 已与 Azure AD 集成。 这使你可通过指定 Azure AD 中的用户在 Azure Databricks 中（例如，在笔记本或群集上）设置权限。 要使 Azure Databricks 能够列出 Azure AD 中的用户名称，它需要该信息的读取权限。 这需要许可。 如果许可尚不可用，将看到错误。

#### <a name="solution"></a>解决方案

以全局管理员身份登录到 Azure 门户。 对于 Azure Active Directory，请转到“用户设置”选项卡并确保“用户可以同意应用代表他们访问公司数据”设置为“是”。

## <a name="next-steps"></a>后续步骤

- [快速入门：Azure Databricks 入门](quickstart-create-databricks-workspace-portal.md)
- [什么是 Azure Databricks？](what-is-azure-databricks.md)

