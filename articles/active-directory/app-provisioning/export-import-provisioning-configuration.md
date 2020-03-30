---
title: 导出预配配置并回滚到已知良好的灾难恢复状态。微软文档
description: 了解如何导出预配配置并回滚到已知良好的灾难恢复状态。
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
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051309"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>导出预配配置并回滚到已知良好状态

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>从 Azure 门户导出和导入预配配置

### <a name="how-can-i-export-my-provisioning-configuration"></a>如何导出预配配置？
要导出配置：
1. 在 [Azure 门户](https://portal.azure.com/)的左侧导航面板中，选择“Azure Active Directory”。****
2. 在**Azure 活动目录**窗格中，选择**企业应用程序**并选择应用程序。
3. 在左侧导航窗格中，选择**预配**。 在预配配置页中，单击**属性映射**，然后**显示高级选项**，最后**查看架构**。 这将带您到架构编辑器。 
5. 单击页面顶部的命令栏中的下载以下载架构。

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>灾难恢复 - 回滚到已知良好的状态
导出和保存配置允许您回滚到配置的早期版本。 我们建议您导出预配配置并将其保存，以便以后对属性映射或范围筛选器进行更改。 您只需打开在上述步骤中下载的 JSON 文件，复制 JSON 文件的全部内容，在架构编辑器中替换 JSON 有效负载的全部内容，然后保存。 如果存在活动预配周期，它将完成，下一个周期将使用更新的架构。 下一个周期也将是初始周期，根据新配置重新评估每个用户和组。 回滚到以前的配置时，请考虑以下事项：
* 将再次评估用户，以确定用户是否应在作用域中。 如果范围筛选器已更改，则用户不再处于作用域内，则用户将被禁用。 虽然在大多数情况下，这是所需的行为，但有时您可能希望防止这种情况，并且可以使用[跳出范围删除](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions)功能。 
* 更改预配配置将重新启动服务并触发[初始周期](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)。


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>使用 Microsoft 图形 API 导出和导入预配配置
您可以使用 Microsoft 图形 API 和 Microsoft 图形资源管理器将用户预配属性映射和架构导出到 JSON 文件并将其导入 Azure AD。 您还可以使用此处捕获的步骤创建预配配置的备份。 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>第 1 步：检索预配应用服务主体 ID（对象 ID）

1. 启动[Azure 门户](https://portal.azure.com)，然后导航到预配应用程序的"属性"部分。 例如，如果要将*工作日导出到 AD 用户预配应用程序映射*，请导航到该应用的"属性"部分。 
1. 在预配应用的“属性”部分中，复制与“对象 ID”字段关联的 GUID 值**。 此值也称为应用的服务**主体Id，** 它将在 Microsoft 图形资源管理器操作中使用。

   ![Workday 应用服务主体 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>第 2 步：登录微软图形资源管理器

1. 启动 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)
1. 单击“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。

    ![微软图形登录](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>第 3 步：检索预配应用的预配作业 ID

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 替换为从[步骤 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 中提取的 ServicePrincipalId****。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

你将获得如下所示的响应。 复制响应中显示的“ID 属性”。 此值为 ProvisioningJobId 且将用于检索基础架构元数据****。

   [![预配作业 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>第 4 步：下载预配架构

在 Microsoft Graph 浏览器中，运行以下 GET 查询，将 [servicePrincipalId] 和 [ProvisioningJobId] 替换为在前述步骤中检索到的 ServicePrincipalId 和 ProvisioningJobId。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

复制响应中的 JSON 对象，并将其保存到要用于创建架构备份的文件中。

### <a name="step-5-import-the-provisioning-schema"></a>第 5 步：导入预配架构

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
