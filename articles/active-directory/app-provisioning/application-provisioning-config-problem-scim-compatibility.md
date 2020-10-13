---
title: 用于跨域标识管理的系统的已知问题 (SCIM) 2.0 协议符合性-Azure AD
description: 如何解决将支持 SCIM 2.0 的非库应用程序添加到 Azure AD 时面临的常见协议兼容性问题
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 7f400d6959a40361ea3beff8bd21c2fa9ef2996a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052624"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 用户预配服务 SCIM 2.0 协议合规性的已知问题和解决方法

Azure Active Directory (Azure AD) 可以使用[跨域身份管理系统 (SCIM) 2.0 协议规范](https://tools.ietf.org/html/draft-ietf-scim-api-19)中定义的接口，将用户和组自动预配到以 Web 服务为前端的任何应用程序或系统。 

Azure AD 对 SCIM 2.0 协议的支持在[使用跨域身份管理系统 (SCIM) 将用户和组从 Azure Active Directory 自动预配到应用程序](use-scim-to-provision-users-and-groups.md)中进行介绍，其中列出为了将用户和组从 Azure AD 自动预配到支持 SCIM 2.0 的应用程序而实现的具体协议部分。

本文介绍 Azure AD 用户预配服务遵循 SCIM 2.0 协议当前和过去面临的问题，以及如何解决这些问题。

## <a name="understanding-the-provisioning-job"></a>了解预配作业
预配服务使用作业的概念来针对应用程序进行操作。 可在 [进度栏](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)中找到 jobID。 所有新的预配应用程序都是使用以 "scim" 开头的 jobID 创建的。 Scim 作业表示服务的当前状态。 旧作业的 ID 为 "customappsso"。 此作业表示服务在2018中的状态。 

如果使用的是库中的应用程序，则作业通常包含应用的名称 (例如，缩放雪花、dataBricks 等 ) 。 使用库应用程序时，可以跳过此文档。 这主要适用于 jobID 为 SCIM 或 customAppSSO 的非库应用程序。

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 合规性问题和状态
在下表中，标记为 "固定" 的任何项都可以在 SCIM 作业中找到正确的行为。 我们努力确保对我们所做的更改的后向兼容性。 但是，我们不建议实现旧行为。 建议为任何新实现使用新行为，并更新现有实现。

> [!NOTE]
> 对于在2018中所做的更改，可以恢复到 customappsso 行为。 对于自2018以来所做的更改，你可以使用 Url 恢复到旧行为。 我们努力确保通过允许还原到旧 jobID 或使用标志，来确保对我们所做的更改的后向兼容性。 然而，如前文所述，我们不建议实现旧行为。 建议为任何新实现使用新行为，并更新现有实现。

| **SCIM 2.0 合规性问题** |  **小数点?** | **修复日期**  |  **后向兼容性** |
|---|---|---|
| Azure AD 要求“/scim”位于应用程序的 SCIM 终结点 URL 的根路径中  | 是  |  2018 年 12 月 18 日 | 降级到 customappSSO |
| 扩展属性在属性名称前使用圆点“.”表示法，而不使用冒号“:”表示法 |  是  | 2018 年 12 月 18 日  | 降级到 customappSSO |
| 多值属性的批量请求包含无效路径筛选器语法 | 是  |  2018 年 12 月 18 日  | 降级到 customappSSO |
| 组创建请求包含无效架构 URI | 是  |  2018 年 12 月 18 日  |  降级到 customappSSO |
| 更新修补行为以确保符合性 (例如，作为布尔值和正确的组成员身份删除)  | 否 | TBD| 使用预览标志 |

## <a name="flags-to-alter-the-scim-behavior"></a>用于更改 SCIM 行为的标志
在应用程序的租户 URL 中使用下面的标志，以更改默认 SCIM 客户端行为。

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="将标志 SCIM 为更高的行为。&quot;:::

* 使用以下 URL 更新修补程序行为，并确保 SCIM 符合性 (例如，作为布尔值进行活动，并) 正确地删除组成员身份。 此行为当前仅在使用标志时才可用，但会成为今后几个月的默认行为。 请注意，此预览标志目前不适用于按需预配。 
  * **URL (符合 SCIM 的) ：** AzureAdScimPatch062020
  * **SCIM RFC 参考：** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **操作**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    &quot;schemas&quot;: [
        &quot;urn:ietf:params:scim:api:messages:2.0:PatchOp&quot;
    ],
    &quot;Operations&quot;: [
        {
            &quot;op&quot;: &quot;remove&quot;,
            &quot;path&quot;: &quot;members[value eq \&quot;16b083c0-f1e8-4544-b6ee-27a28dc98761\&quot;]&quot;
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    &quot;schemas&quot;: [
        &quot;urn:ietf:params:scim:api:messages:2.0:PatchOp&quot;
    ],
    &quot;Operations&quot;: [
        {
            &quot;op&quot;: &quot;add&quot;,
            &quot;path&quot;: &quot;members&quot;,
            &quot;value&quot;: [
                {
                    &quot;value&quot;: &quot;10263a6910a84ef9a581dd9b8dcc0eae&quot;
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    &quot;schemas&quot;: [
        &quot;urn:ietf:params:scim:api:messages:2.0:PatchOp&quot;
    ],
    &quot;Operations&quot;: [
        {
            &quot;op&quot;: &quot;replace&quot;,
            &quot;path&quot;: &quot;emails[type eq \&quot;work\&quot;].value&quot;,
            &quot;value&quot;: &quot;someone@contoso.com&quot;
        },
        {
            &quot;op&quot;: &quot;replace&quot;,
            &quot;path&quot;: &quot;emails[type eq \&quot;work\&quot;].primary&quot;,
            &quot;value&quot;: true
        },
        {
            &quot;op&quot;: &quot;replace&quot;,
            &quot;value&quot;: {
                &quot;active&quot;: false,
                &quot;userName&quot;: &quot;someone&quot;
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    &quot;schemas&quot;: [
        &quot;urn:ietf:params:scim:api:messages:2.0:PatchOp&quot;
    ],
    &quot;Operations&quot;: [
        {
            &quot;op&quot;: &quot;replace&quot;,
            &quot;path&quot;: &quot;active&quot;,
            &quot;value&quot;: false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    &quot;schemas&quot;: [
        &quot;urn:ietf:params:scim:api:messages:2.0:PatchOp&quot;
    ],
    &quot;Operations&quot;: [
        {
            &quot;op&quot;: &quot;add&quot;,
            &quot;path&quot;: &quot;urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department&quot;,
            &quot;value&quot;: &quot;Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **降级 URL：** 在非库应用程序上将新的 SCIM 兼容行为变为默认值后，可以使用以下 URL 回滚到旧的、不符合 SCIM 的行为： AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>从较旧的 customappsso 作业升级到 SCIM 作业
按照以下步骤操作将删除现有的 customappsso 作业，并创建新的 scim 作业。 
 
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

8. 复制最后一步的 JSON 输出，并保存到文本文件。 JSON 包含你添加到旧应用的任何自定义属性映射，并且应约为数千行 JSON。
9. 运行以下命令，删除预配作业：
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 运行以下命令，创建具有最新服务修补程序的新预配作业。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 在最后一步的结果中，复制以“scim”开头的完整“ID”字符串。 还可以通过运行以下命令重新应用旧的属性映射，将 [new-job id] 替换为复制的新作业 ID，并输入步骤 #7 的 JSON 输出作为请求正文。

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 返回第一个 Web 浏览器窗口，选择应用程序的“预配”选项卡****。
13. 验证配置，然后启动预配作业。 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>不建议将 SCIM 作业从作业降级到 customappsso 作业 () 
 我们允许降级到旧行为，但不建议这样做，因为 customappsso 不能从我们所做的某些更新中获益，而且可能不会被永久支持。 

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
