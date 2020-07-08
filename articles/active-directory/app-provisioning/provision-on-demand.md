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
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297522"
---
# <a name="on-demand-provisioning"></a>按需预配
按需预配允许用户在几秒钟内将用户预配到应用程序。 您可以使用此功能快速排查配置问题、验证您定义的表达式、测试范围筛选器以及更多内容。 

## <a name="how-to-use-on-demand-provisioning"></a>如何使用按需预配 

1. 登录到 **Azure 门户**。
2. 导航到“企业应用程序”。****
3. 选择应用程序并导航到 "配置" 配置页。
4. 通过提供管理员凭据来配置设置。
5. 单击 **"按需预配"**。
6. 按名字、姓氏、显示名称、用户主体名称或电子邮件搜索用户。
7. 选择页面底部的 "预配"。

## <a name="understanding-the-provisioning-steps"></a>了解预配步骤
按需预配功能尝试在预配用户时显示预配服务所需的步骤。 预配用户通常需要5个步骤，并且按需预配体验中将显示以下一个或多个步骤。

### <a name="step-1-test-connection"></a>步骤1：测试连接
预配服务会通过请求 "测试用户" 来尝试对目标应用程序的访问权限。 预配服务需要一个响应，指示其已获得授权，可继续进行预配步骤。 仅当步骤中出现故障时才会显示此步骤。 如果步骤成功，则不显示按需预配体验。 

**故障排除提示**
* 确保你向目标应用程序提供了有效凭据，如机密令牌和租户 URL。 所需的凭据因应用程序而异。 可在[此处](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)找到详细的配置教程。 
* 确保目标应用程序支持对 "属性映射" 边栏选项卡中定义的匹配属性进行筛选。 你可能需要检查应用程序开发人员提供的 API 文档，以了解它们支持的筛选器。  
* 对于 SCIM 应用程序，可以使用 postman 等工具来确保应用程序响应 Azure AD 预配服务预期的授权请求。 可在[此处](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3)找到示例请求。

### <a name="step-2-import-user"></a>步骤2：导入用户
接下来，预配服务从源系统检索用户。 服务检索的用户属性稍后用于评估用户是否处于预配范围内，检查现有用户的目标系统，以及确定要导出到目标系统的用户属性。 

**查看详细信息**

"查看详细信息" 部分显示从源系统导入的用户的属性（例如 Azure AD）。

**故障排除提示**
* 如果源系统中的用户对象缺少匹配的属性，则导入用户可能会失败。 您可以通过使用匹配属性的值更新用户对象，或在设置配置中更改匹配属性来解决此问题。  
* 如果导入的列表中缺少所需的属性，请确保该属性在源系统中的用户对象上具有值。 预配服务当前不支持预配 null 属性。 
* 确保 "设置配置属性映射" 页包含所需的属性。 

### <a name="step-3-determine-if-user-is-in-scope"></a>步骤3：确定用户是否在范围内
接下来，预配服务确定用户是否处于预配的[范围](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping)内。 服务将考虑一些因素，例如是否将用户分配到应用程序、是否将作用域设置为 "已分配同步" 或 "全部同步"，以及在预配配置中定义的范围筛选器。  

**查看详细信息**

"查看详细信息" 部分显示已评估的范围条件。 你可能会看到以下一个或多个属性：
* **源系统中的活动**指示用户在 Azure AD 中将属性设置为 "活动"。
* **分配给应用程序**指示用户已分配到 Azure AD 中的应用程序
* **作用域同步所有**指示范围设置允许租户中的所有用户和组。
* **用户具有所需角色**表示用户具有要预配到应用程序中的必要角色。 
* 如果已为应用程序定义了范围筛选器，也会显示**范围筛选器**。 将显示以下格式的筛选器： {范围筛选器标题} {范围筛选器属性} {范围筛选器运算符} {范围筛选值}。 

**故障排除提示**
* 确保你定义了有效的范围角色。 例如，避免将["大于"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter)运算符用于非整数值。 
* 如果用户没有必要的角色，请查看[此处](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)所述的提示。 

### <a name="step-4-match-user-between-source-and-target"></a>步骤4：在源和目标之间匹配用户
在此步骤中。 服务尝试将导入步骤中检索的用户与目标系统中的用户匹配。 

**查看详细信息**

"查看详细信息" 页显示在目标系统中匹配的用户的属性。 上下文窗格中显示的属性如下所示：
* 如果目标系统中没有匹配的用户，则将看不到任何属性。
* 如果目标系统中有一个用户匹配，您将看到目标系统中匹配用户的属性。
* 如果匹配多个用户，你将看到两个匹配的用户的属性。
* 如果有多个匹配属性属于您的属性映射，则每个匹配属性将按顺序计算并显示匹配的用户。 

**疑难解答详细信息**
* 预配服务无法将源中的用户与目标中的用户唯一匹配。 可以通过确保匹配属性是唯一的来解决此情况。 
* 确保目标应用程序支持对定义为匹配属性的属性进行筛选。  

### <a name="step-5-perform-action"></a>步骤5：执行操作
最后，预配服务会采取措施，如创建、更新、删除或跳过用户。 

**查看详细信息**

"查看详细信息" 部分显示在目标应用程序中修改的属性。 这表示预配服务活动和导出的属性的最终输出。 如果此步骤失败，则显示的属性表示预配服务尝试修改的属性。  

**故障排除提示**
* 导出更改失败可能会有很大差异。 有关常见故障，请查看配置日志[文档](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes)。


## <a name="frequently-asked-questions"></a>常见问题
**是否需要关闭预配以使用按需预配？** 对于使用长生存期持有者令牌或用户名和密码进行授权的应用程序，无需执行其他步骤。 使用 OAuth 进行授权的应用程序当前要求在使用按需预配之前停止预配作业。 应用程序（如 G Suite、Box、Workplace by Facebook 和时差）属于此类别。 正在进行的工作使所有应用程序都可以运行按需预配，而不必停止预配。 

**按需预配需要多长时间？** 通常不超过30秒。 

## <a name="known-limitations"></a>已知限制
目前有几个已知的限制。 请在[UserVoice](https://aka.ms/appprovisioningfeaturerequest)上发布，以便我们可以更好地确定要进行的改进的优先级。 请注意，这些限制特定于按需预配功能。 有关应用程序是否支持预配组、删除等操作的详细信息，请查看应用程序教程。 

* 应用程序 Workday、AWS 和 SuccessFactors 不支持按需预配。
* 不支持按需预配组和角色。
* 不支持禁用或删除用户和组。

## <a name="next-steps"></a>后续步骤

* [疑难解答预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
