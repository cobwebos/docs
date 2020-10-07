---
title: 向 Azure AD B2C 中的用户流添加条件访问
description: 了解如何向 Azure AD B2C 用户流添加条件访问。 在用户流中配置多重身份验证 (MFA) 设置和条件访问策略，以强制实施策略并修正风险登录。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfac3b80e772e7b359b1e926d5fb84e447a8fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270679"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>向 Azure Active Directory B2C 中的用户流添加条件访问

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

可以将条件访问添加到 Azure Active Directory B2C 用户流，以管理应用程序的风险登录。 通过 Azure AD B2C 中的标识保护和条件访问集成，可以设置识别风险登录行为的策略，并强制实施需要用户或管理员执行进一步操作的策略。 以下是在 Azure AD B2C 用户流中启用条件访问的组件：

- **用户流**。 创建可指导用户完成登录和注册过程的用户流。 在用户流设置中，指示当用户遵循用户流时是否激活条件访问策略。
- **将用户定向到用户流的应用程序**。 通过在应用中指定用户流终结点，配置应用以将用户定向到相应的注册和登录用户流。
- **条件访问策略**。 [创建条件访问策略](conditional-access-identity-protection-setup.md)，并指定要将策略应用到的应用。 当用户遵循应用的登录或注册用户流时，条件访问策略将使用标识保护信号来识别风险登录，并在必要时提供适当的修正措施。

最新版本的用户流支持条件访问。 可以在创建新用户流时向新用户流添加条件访问策略，也可以将条件访问策略添加到现有用户流中，只要版本支持条件访问。 向现有用户流添加条件访问时，需要查看两个设置：

- **多重身份验证 (MFA)** ：用户现在可以通过短信或语音使用一次性代码，或通过电子邮件使用一次性密码进行多重身份验证。 MFA 设置独立于条件访问设置。 可以将 MFA 设置为“始终启用”，这样无论你的条件访问设置如何，都始终需要 MFA。 或者，可以将 MFA 设置为“条件性”，使得仅在活动条件访问策略要求 MFA 时才进行 MFA。

- **条件访问**：此设置应始终为“启用”。 通常情况下，仅在故障排除或迁移过程中或针对旧式实现关闭此设置。

详细了解 Azure AD B2C 中的[标识保护和条件访问](conditional-access-identity-protection-overview.md)或了解[如何进行相关设置](conditional-access-identity-protection-setup.md)。

## <a name="add-conditional-access-to-a-new-user-flow"></a>向新用户流添加条件访问

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，依次选择“用户流”、“新建用户流”。
1. 在“创建用户流”页上，选择用户流类型。
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）

    ![在 Azure 门户中创建用户流页面（其中，属性突出显示）](./media/tutorial-create-user-flows/select-version.png)

1. 输入该用户流的**名称**。 例如 *signupsignin1*。
1. 在“标识提供者”部分中，选择要允许此用户流使用的标识提供者。
2. 在“多重身份验证”部分中，选择所需的“MFA 方法”，然后在“MFA 强制执行”下选择“条件(推荐)”   。
 
   ![配置多重身份验证](media/conditional-access-user-flow/configure-mfa.png)

1. 在“条件访问”部分中，选择“强制实施条件访问策略”复选框 。

   ![配置条件访问设置](media/conditional-access-user-flow/configure-conditional-access.png)

1. 在“用户属性和声明”部分中，请选择在注册期间要从用户收集和发送的声明和属性。 例如，选择“显示更多”，然后选择“国家/地区”和“显示名称”的属性和声明  。 选择“确定”。 

    ![属性和声明选择页，有三个声明处于选中状态](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. 单击“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

## <a name="add-conditional-access-to-an-existing-user-flow"></a>向现有用户流添加条件访问

> [!NOTE]
> 现有的用户流必须是支持条件访问的版本。 这些用户流版本标记为“推荐”。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。

1. 在“策略”下，选择“用户流” 。 然后选择用户流。

1. 选择“属性”，并通过选择“属性”并查找标记为“条件访问”的设置，确保用户流支持条件访问  。
 
   ![在“属性”中配置 MFA 和条件访问](media/conditional-access-user-flow/add-conditional-access.png)

1. 在“多重身份验证”部分中，选择所需的“MFA 方法”，然后在“MFA 强制执行”下选择“条件(推荐)”   。
 
1. 在“条件访问”部分中，选择“强制实施条件访问策略”复选框 。

1. 选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

若要在用户流中测试条件访问，请[创建条件访问策略](conditional-access-identity-protection-setup.md)，并在用户流中启用条件访问，如上所述。 

### <a name="prerequisites"></a>先决条件

- 创建风险登录策略需要 Azure AD B2C Premium 2。 Premium P1 租户可创建位置、应用或基于组的策略。
- 出于测试目的，可[注册测试 Web 应用程序](tutorial-register-applications.md) `https://jwt.ms`，这是 Microsoft 拥有的 Web 应用，用于显示令牌的已解码内容（令牌的内容绝不会离开浏览器）。 
- 若要模拟风险登录，请下载 TOR 浏览器并尝试登录到用户流终结点。
- 使用以下设置[创建条件访问策略](conditional-access-identity-protection-setup.md)：
   
   - 对于“用户和组”，请选择测试用户（不要选择“所有用户”，否则将无法登录） 。
   - 对于“云应用或操作”，选择“选择应用”，然后选择依赖方应用程序 。
   - 对于“条件”，选择“登录风险”以及“高”、“中”和“低”风险级别   。
   - 对于“授权”，选择“阻止访问” 。

      ![风险检测](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>运行用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。 在“应用程序”下，选择“webapp1”。 “回复 URL”应显示为 `https://jwt.ms`。

   ![门户中的“运行用户流”页面，突出显示了“运行用户流”按钮](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 复制“运行用户流终结点”下的 URL。

1. 要模拟风险登录，请打开 [Tor 浏览器](https://www.torproject.org/download/)，并使用在预览步骤中复制的 URL 登录到已注册的应用。

1. 在登录页中输入请求的信息，然后尝试登录。 令牌将返回到 `https://jwt.ms` 并显示出来。 在 jwt.ms 解码令牌中，你应看到登录已被阻止：

   ![测试阻止的登录](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>后续步骤

[在 Azure AD B2C 用户流中自定义用户界面](customize-ui-overview.md)
