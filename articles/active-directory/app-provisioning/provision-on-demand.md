---
title: 使用 Azure Active Directory 按需预配用户
description: 强制同步
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: f7cbe9e9f81b3b71ee7da2feac2908c36f1777e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629006"
---
# <a name="on-demand-provisioning"></a>按需预配
使用按需预配可在数秒内将用户预配到应用程序。 除此之外，还可以使用此功能执行以下操作：

* 快速解决配置问题。
* 验证已定义的表达式。
* 测试范围筛选器。

## <a name="how-to-use-on-demand-provisioning"></a>如何使用按需预配

1. 登录 **Azure 门户**。
1. 中转到 "**所有服务**" "  >  **企业应用程序**"。
1. 选择应用程序，然后打开 "设置配置" 页。
1. 通过提供管理员凭据来配置设置。
1. 选择 **"按需预配"**。
1. 按名字、姓氏、显示名称、用户主体名称或电子邮件地址搜索用户。
   > [!NOTE]
   > 对于云 HR 预配应用 (Workday/SuccessFactors 到 AD/Azure AD) ，输入值不同。 对于 Workday 方案，请在 Workday 中提供用户的 "WID"。 对于 SuccessFactors 方案，请在 SuccessFactors 中提供用户的 "personIdExternal"。 
 
1. 选择页面底部的 " **预配** "。

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="显示用于按需预配用户的 Azure 门户 UI 的屏幕截图。":::

## <a name="understand-the-provisioning-steps"></a>了解预配步骤

按需预配过程会尝试显示预配服务在预配用户时所执行的步骤。 预配用户通常需要5个步骤。 在按需预配体验期间，将显示以下部分中所述的一个或多个步骤。

### <a name="step-1-test-connection"></a>步骤1：测试连接

预配服务会通过请求 "测试用户" 来尝试对目标应用程序的访问权限。 预配服务需要一个响应，指示已获得授权，可继续执行预配步骤。 此步骤只有在失败时才会显示。 如果步骤成功，则不显示按需预配体验。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 确保向目标应用程序提供了有效凭据，如机密令牌和租户 URL。 所需的凭据因应用程序而异。 有关详细的配置教程，请参阅 [教程列表](../saas-apps/tutorial-list.md)。 
* 请确保目标应用程序支持对 " **属性映射** " 窗格中定义的匹配属性进行筛选。 你可能需要检查应用程序开发人员提供的 API 文档，以了解支持的筛选器。
* 对于跨域标识管理 (SCIM) 应用程序的系统，可以使用 Postman 等工具。 此类工具可帮助你确保应用程序以 Azure Active Directory (Azure AD) 预配服务预期的方式响应授权请求。 查看 [示例请求](./use-scim-to-provision-users-and-groups.md#request-3)。

### <a name="step-2-import-user"></a>步骤2：导入用户

接下来，预配服务从源系统检索用户。 服务检索的用户属性稍后将用于：

* 评估用户是否处于预配的范围内。
* 检查现有用户的目标系统。
* 确定要导出到目标系统的用户属性。

#### <a name="view-details"></a>查看详细信息


" **查看详细信息** " 部分显示从源系统导入的用户的属性，例如 Azure AD)  (。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 如果源系统中的用户对象缺少匹配的属性，则导入用户可能会失败。 若要解决此失败，请尝试以下方法之一：

  * 使用匹配属性的值更新用户对象。
  * 更改设置配置中的匹配属性。

* 如果导入的列表中缺少所需的属性，请确保该属性在源系统中的用户对象上具有值。 预配服务当前不支持预配 null 属性。
* 请确保预配配置的 " **属性映射** " 页包含所需的属性。

### <a name="step-3-determine-if-user-is-in-scope"></a>步骤3：确定用户是否在范围内

接下来，预配服务确定用户是否处于预配的 [范围](./how-provisioning-works.md#scoping) 内。 该服务会考虑以下几个方面：

* 是否已将用户分配到应用程序。
* 作用域是设置为 **已分配同步** 还是 **全部同步**。
* 设置配置中定义的范围筛选器。  

#### <a name="view-details"></a>查看详细信息

" **查看详细信息** " 部分显示已评估的范围条件。 你可能会看到以下一个或多个属性：

* **源系统中的活动** 指示用户 `IsActive` 在 Azure AD 中将属性设置为 **true** 。
* "**分配给应用程序**" 指示将用户分配到 Azure AD 中的应用程序。
* **作用域同步所有** 指示范围设置允许租户中的所有用户和组。
* **用户具有所需角色** 表示用户具有要预配到应用程序中的必要角色。 
* 如果已为应用程序定义了范围筛选器，也会显示**范围筛选器**。 此筛选器以以下格式显示： {范围筛选器标题} {范围筛选器属性} {范围筛选器运算符} {范围筛选器值}。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 请确保定义了有效的范围角色。 例如，避免将 [Greater_Than 运算符](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) 用于非整数值。
* 如果用户没有必要的角色，请查看 [有关设置分配给默认访问角色的用户的提示](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)。

### <a name="step-4-match-user-between-source-and-target"></a>步骤4：在源和目标之间匹配用户

在此步骤中，该服务将尝试将导入步骤中检索的用户与目标系统中的用户匹配。

#### <a name="view-details"></a>查看详细信息

" **查看详细信息** " 页显示在目标系统中匹配的用户的属性。 上下文窗格中显示的属性如下所示：

* 如果目标系统中没有匹配的用户，则将看不到任何属性。
* 如果目标系统中有一个用户匹配，则会看到目标系统中匹配用户的属性。
* 如果匹配多个用户，则会看到两个匹配的用户的属性。
* 如果多个匹配属性属于您的属性映射，则每个匹配属性将按顺序进行计算，并显示该属性的匹配用户。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 预配服务可能无法与目标系统中的用户唯一匹配源系统中的用户。 若要解决此问题，请确保匹配属性是唯一的。
* 请确保目标应用程序支持对定义为匹配属性的属性进行筛选。  

### <a name="step-5-perform-action"></a>步骤5：执行操作

最后，预配服务会采取措施，如创建、更新、删除或跳过用户。

下面是在成功按需预配用户之后可能会看到的示例：

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="显示用于按需预配用户的 Azure 门户 UI 的屏幕截图。":::

#### <a name="view-details"></a>查看详细信息

" **查看详细信息** " 部分显示在目标应用程序中修改的属性。 此显示表示预配服务活动的最终输出和导出的属性。 如果此步骤失败，则显示的属性表示预配服务尝试修改的属性。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 导出更改失败可能会有很大差异。 请查看 [文档，](../reports-monitoring/concept-provisioning-logs.md#error-codes) 了解常见故障的设置日志。

## <a name="frequently-asked-questions"></a>常见问题解答

* **是否需要关闭预配以使用按需预配？** 对于使用长生存期持有者令牌或用于授权的用户名和密码的应用程序，无需执行其他步骤。 使用 OAuth 进行授权的应用程序当前需要在使用按需预配之前停止预配作业。 应用程序（如 G Suite、Box、Workplace by Facebook 和时差）属于此类别。 工作正在进行中，支持所有应用程序的按需预配，无需停止预配作业。

* **按需预配需要多长时间？** 按需预配通常需要不到30秒的时间。

## <a name="known-limitations"></a>已知的限制

对于按需预配，当前存在一些已知限制。 发布你的 [建议和反馈](https://aka.ms/appprovisioningfeaturerequest) ，以便我们可以更好地确定要进行的改进。

> [!NOTE]
> 以下限制特定于按需预配功能。 有关应用程序是否支持预配组、删除或其他功能的信息，请参阅该应用程序的教程。

* Amazon Web Services (AWS) 应用程序不支持按需预配。 
* 不支持按需预配组和角色。
* 按需预配支持禁用从应用程序中取消分配的用户。 但是，它不支持禁用或删除已从 Azure AD 中禁用或删除的用户。 搜索用户时不会显示这些用户。

## <a name="next-steps"></a>后续步骤

* [疑难解答预配](./application-provisioning-config-problem.md)