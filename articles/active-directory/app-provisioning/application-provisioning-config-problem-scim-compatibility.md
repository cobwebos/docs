---
title: SCIM 2.0 协议符合性的已知问题-Azure AD
description: 如何解决将支持 SCIM 2.0 的非库应用程序添加到 Azure AD 时面临的常见协议兼容性问题
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 00551eb6c9d5d6fab9dc1d698a7a25bb6872901b
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594026"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 用户预配服务 SCIM 2.0 协议合规性的已知问题和解决方法

Azure Active Directory (Azure AD) 可以使用[跨域身份管理系统 (SCIM) 2.0 协议规范](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定义的接口，将用户和组自动预配到以 Web 服务为前端的任何应用程序或系统。 

Azure AD 对 SCIM 2.0 协议的支持在[使用跨域身份管理系统 (SCIM) 将用户和组从 Azure Active Directory 自动预配到应用程序](use-scim-to-provision-users-and-groups.md)中进行介绍，其中列出为了将用户和组从 Azure AD 自动预配到支持 SCIM 2.0 的应用程序而实现的具体协议部分。

本文介绍 Azure AD 用户预配服务遵循 SCIM 2.0 协议当前和过去面临的问题，以及如何解决这些问题。

> [!IMPORTANT]
> Azure AD 用户预配服务 SCIM 客户端的最新更新于 2018 年 12 月 18 日发布。 此更新解决了下表中列出的已知兼容性问题。 有关此更新的详细信息，请参阅下文的常见问题解答。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 合规性问题和状态

| **SCIM 2.0 合规性问题** |  **小数点?** | **修复日期**  |  
|---|---|---|
| Azure AD 要求“/scim”位于应用程序的 SCIM 终结点 URL 的根路径中  | 是  |  2018 年 12 月 18 日 | 
| 扩展属性在属性名称前使用圆点“.”表示法，而不使用冒号“:”表示法 |  是  | 2018 年 12 月 18 日  | 
|  多值属性的批量请求包含无效路径筛选器语法 | 是  |  2018 年 12 月 18 日  | 
|  组创建请求包含无效架构 URI | 是  |  2018 年 12 月 18 日  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>所述服务修补程序是否会自动应用于预先存在的 SCIM 应用？

否。 由于已构成对编码为与较旧行为配合使用的 SCIM 应用的重大更改，更改不会自动应用于现有应用。

修补程序发布后，更改会应用于 Azure 门户中配置的所有新的[非库 SCIM 应用](../manage-apps/configure-single-sign-on-non-gallery-applications.md)。

有关如何迁移预先存在的用户预配作业以包含最新修补程序的信息，请参阅下一部分。

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>是否可以迁移现有的基于 SCIM 的用户预配作业以包含最新的服务修补程序？

是的。 如果已经在为单一登录使用此应用程序实例，并需要迁移现有预配作业以包含最新修补程序，请执行下方程序。 此程序介绍如何使用 Microsoft Graph API 和 Microsoft Graph API 资源管理器从现有 SCIM 应用删除旧的预配作业，以及如何创建展示新行为的新预配作业。

> [!NOTE]
> 如果应用程序仍处于开发阶段，并且尚未针对单一登录或用户预配进行部署，最简单的解决办法是在 Azure 门户的“Azure Active Directory”>“企业应用程序”部分中删除应用程序条目，然后使用“创建应用程序”>“非库”选项直接添加新应用程序条目********。 这是执行以下程序的替代方法。
 
1. 登录 Azure 门户：https://portal.azure.com。
2. 在 Azure 门户的“Azure Active Directory”>“企业应用程序”部分，找到并选择现有 SCIM 应用程序****。
3. 在现有 SCIM 应用的“属性”部分，复制“对象 ID”********。
4. 在新的 Web 浏览器窗口中，转到 https://developer.microsoft.com/graph/graph-explorer 并以要向其中添加应用的 Azure AD 租户的管理员身份登录。
5. 在 Graph 资源管理器中，运行以下命令以找到预配作业的 ID。 将“[object-id]”替换为从第三步复制的服务主体 ID（对象 ID）。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![获取作业](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "获取作业") 


6. 在结果中，复制以“customappsso”或“scim”开头的完整“ID”字符串。
7. 运行以下命令，检索属性映射配置，以便执行备份。 使用和之前一样的 [object-id]，并将 [job-id] 替换为从最后一步复制的预配作业 ID。
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![获取架构](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "获取架构") 

8. 复制最后一步的 JSON 输出，并保存到文本文件。 此输出包含向旧应用添加的所有自定义属性映射，应该约有数千行 JSON。
9. 运行以下命令，删除预配作业：
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 运行以下命令，创建具有最新服务修补程序的新预配作业。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 在最后一步的结果中，复制以“scim”开头的完整“ID”字符串。 可选择运行以下命令，将 [new-job-id] 替换为刚才复制的新作业 ID，并输入第七步的 JSON 输出作为请求正文来重新应用旧属性映射。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 返回第一个 Web 浏览器窗口，选择应用程序的“预配”选项卡****。
13. 验证配置，然后启动预配作业。 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>是否可以添加具有旧用户预配行为的新非库应用？

是的。 如果已将应用程序编码为展示修补程序发布前已经存在的旧行为，并需要部署其新实例，请执行以下程序。 此程序介绍如何使用 Microsoft Graph API 和 Microsoft Graph API 资源管理器创建展示旧行为的 SCIM 预配作业。
 
1. 登录 Azure 门户：https://portal.azure.com。
2. 在 Azure 门户的“Azure Active Directory”>“企业应用程序”>“创建应用程序”部分，创建新的“非库”应用程序********。
3. 在新的自定义应用的“属性”部分，复制“对象 ID”********。
4. 在新的 Web 浏览器窗口中，转到 https://developer.microsoft.com/graph/graph-explorer 并以要向其中添加应用的 Azure AD 租户的管理员身份登录。
5. 在 Graph 资源管理器中，运行以下命令，初始化应用的预配配置。
   将“[object-id]”替换为从第三步复制的服务主体 ID（对象 ID）。

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 返回第一个 Web 浏览器窗口，选择应用程序的“预配”选项卡****。
7. 照常完成用户预配配置。


## <a name="next-steps"></a>后续步骤
[了解关于 SaaS 应用程序预配和取消预配的详细信息](user-provisioning.md)

