---
title: 使用 Microsoft Graph API 导出或导入预配配置 |Microsoft Docs
description: 了解如何使用 Microsoft Graph API 导出和导入预配配置。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2fa80726875c82cfa4b5d4cf6a14f4e0dae1871
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367799"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>使用 Microsoft Graph API 导出或导入预配配置

你可以使用 Microsoft Graph API 和 Microsoft Graph 资源管理器将用户预配属性映射和架构导出到 JSON 文件，然后将其导入回 Azure AD 中。 你还可以使用此处捕获的步骤来创建预配配置的备份。 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步骤 1：检索预配应用服务主体 ID （对象 ID）

1. 启动[Azure 门户](https://portal.azure.com)，并导航到预配应用程序的 "属性" 部分。 例如，如果要将*Workday 导出到 AD 用户预配应用程序*映射，请导航到该应用的 "属性" 部分。 
1. 在预配应用的“属性”部分中，复制与“对象 ID”字段关联的 GUID 值。 此值也称为应用的**ServicePrincipalId** ，它将用于 Microsoft Graph 资源管理器操作。

   ![Workday 应用服务主体 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步骤 2：登录到 Microsoft Graph 浏览器

1. 启动 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)
1. 单击“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。

    ![Microsoft Graph 登录](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>步骤 3：检索预配应用的设置作业 ID

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 中提取的 ServicePrincipalId。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

你将获得如下所示的响应。 复制响应中显示的“ID 属性”。 此值为 ProvisioningJobId 且将用于检索基础架构元数据。

   [![设置作业 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>步骤 4：下载预配架构

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 和 [ProvisioningJobId] 替换为在前述步骤中检索到的 ServicePrincipalId 和 ProvisioningJobId。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

复制响应中的 JSON 对象，并将其保存到要用于创建架构备份的文件中。

## <a name="step-5-import-the-provisioning-schema"></a>步骤 5：导入预配架构

> [!CAUTION]
> 仅当需要修改无法使用 Azure 门户更改的配置架构，或者需要通过带有有效且正在工作的架构的先前备份文件还原配置时，才执行此步骤。

在 Microsoft Graph 浏览器中，配置以下 PUT 查询，将 [servicePrincipalId] 和 [ProvisioningJobId] 替换为在前述步骤中检索到的 ServicePrincipalId 和 ProvisioningJobId。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

在“请求正文”选项卡中，复制 JSON 架构文件的内容。

   [![请求正文](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

在“请求标头”选项卡中，添加值为“application/json”的 Content-Type 标头属性

   [![请求标头](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

单击“运行查询”按钮来导入新的架构。
