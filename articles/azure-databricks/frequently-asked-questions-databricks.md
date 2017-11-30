---
title: "Azure Databricks：常见问题和帮助 | Microsoft Docs"
description: "获取有关 Azure Databricks 的常见问题的答案和故障诊断信息。"
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Azure Databricks 预览版：常见问题和帮助

本文列出了用户可能会有的与 Azure Databricks 相关的几大疑问。 同时还列出了用户在使用 Azure Databricks 时可能会遇到到一些常见问题。 有关 Azure Databricks 的详细信息，请参阅[什么是 Azure Databricks？](what-is-azure-databricks.md) 

## <a name="common-questions"></a>常见问题

本部分列出了与 Azure Databricks 相关的常见问题。

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>能否使用我自己的密钥进行本地加密？ 
目前的版本不支持使用 Azure Key Vault 中你自己的密钥。 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>能否将 Azure VNET 与 Azure Databricks 配合使用？
Azure Databricks 预配过程中会创建新的 VNET。 不能在此版本中使用你自己的 Azure VNET。

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>如何使用笔记本访问 Azure Data Lake Store？ 

1. 在 Azure Active Directory 中预配一个服务主体并记录其密钥。
2. 在 Azure Data Lake Store 中分配服务主体的必需权限。
3. 若要访问 Azure Data Lake Store 中的文件，请使用笔记本中的服务主体凭据。

有关详细信息，请参阅[配合使用 Data Lake Store 和 Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)。

## <a name="troubleshooting"></a>故障排除

本部分介绍如何排查 Azure Databricks 的常见问题。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>问题：该订阅未注册为使用命名空间“Microsoft.Databricks”

**错误消息**

该订阅未注册为使用命名空间“Microsoft.Databricks”。 请参阅 https://aka.ms/rps-not-found 了解如何注册订阅。 （代码：MissingSubscriptionRegistration）

**解决方案**

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“订阅”、正在使用的订阅，然后单击“资源提供程序”。 
3. 在资源提供程序列表中，针对“Microsoft.Databricks”单击“注册”。 必须具有订阅的参与者或所有者角色才能注册资源提供程序。


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>问题：在 Azure 门户中，你的帐户 {电子邮件} 没有 Databricks 工作区资源的“所有者”或“参与者”角色。

**错误消息**

在 Azure 门户中，你的帐户 {电子邮件} 没有 Databricks 工作区资源的“所有者”或“参与者”角色。 如果你是租户中的来宾用户，也可能发生此错误。 要求管理员授予你访问权限，或者直接在 Databricks 工作区中将你添加为 A 用户。 

**解决方案**

以下是此问题的一些解决方案：

* 若要初始化租户，必须以租户的常规用户（而非来宾用户）身份登录。 还必须具有 Databricks 工作区资源的“参与者”角色。 可以在 Azure 门户的 Azure Databricks 工作区中，通过“访问控制(IAM)”选项卡授予用户访问权限。

* 如果电子邮件域名被分配给多个 Active Directory，也可能会发生此错误。 若要解决此问题，可在包含订阅和 Databricks 工作区的 Active Directory 中创建新的用户。

    a. 在 Azure 门户中，转到 Azure Active Directory，单击“用户和组”，单击“添加用户”。

    b. 使用 `@<tenant_name>.onmicrosoft.com` 电子邮件而非 @<你的_域> 电子邮件添加用户。 可在 Azure 门户的 Azure Active Directory 下的“自定义域”中找到与你的 Active Directory 相关联的 <租户_名称>.onmicrosoft.com。
    
    c. 授予新用户 Databricks 工作区资源的“参与者”角色。
    
    d.单击“下一步”。 使用新用户登录到 Azure 门户，并找到 Databricks。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 以此用户的身份启动 Databricks 工作区。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>问题：你的帐户 {电子邮件} 未在 Databricks 中注册 

**解决方案**

如果你未创建该工作区，而你是作为工作区的用户添加的，请联系创建该工作区的人，让其使用 Azure Databricks 管理控制台来添加你。 有关说明，请参阅 [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)（添加和管理用户）。 如果是你创建了该工作区而仍然出现此错误，请尝试再次在 Azure 门户中单击“初始化工作区”。

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>问题：云提供程序启动故障 (PublicIPCountLimitReached)：在设置群集时遇到云提供程序错误

**错误消息**

云提供程序启动故障：在设置群集时遇到云提供程序错误。 请参阅 Databricks 指南以获取详细信息。 Azure 错误代码：PublicIPCountLimitReached。 Azure 错误消息：不能在此区域为该订阅创建超过 60 个公共 IP 地址。

**解决方案**

Azure Databricks 群集为每个节点使用一个公共 IP 地址。 如果订阅已使用其所有的公共 IP，则应[请求增加配额](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request)。 选择“配额”作为“问题类型”，选择“网络: ARM”作为“配额类型”，并在“详细信息”中请求增加公用 IP 地址配额。 例如，如果限制当前为 60，你想要创建具有 100 个节点的群集，则请求将限制增加至 160。

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>问题：云提供程序启动故障 (MissingSubscriptionRegistration)：在设置群集时遇到云提供程序错误

**错误消息**

云提供程序启动故障：在设置群集时遇到云提供程序错误。 请参阅 Databricks 指南以获取详细信息。
Azure 错误代码：MissingSubscriptionRegistration；Azure 错误消息：订阅未注册为使用命名空间“Microsoft.Databricks”。 请参阅 https://aka.ms/rps-not-found 了解如何注册订阅

**解决方案**

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 依次单击“订阅”、正在使用的订阅，然后单击“资源提供程序”。 
3. 在资源提供程序列表中，针对“Microsoft.Compute”单击“注册”。 必须具有订阅的参与者或所有者角色才能注册资源提供程序。

请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)了解详细说明。

## <a name="next-steps"></a>后续步骤
有关创建版本 2 的数据工厂的分步说明，请参阅以下教程：

- [快速入门：Azure Databricks 入门](quickstart-create-databricks-workspace-portal.md)
- [什么是 Azure Databricks？](what-is-azure-databricks.md)

