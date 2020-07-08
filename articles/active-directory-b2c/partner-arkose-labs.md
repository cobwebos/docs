---
title: Arkose 实验室与 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 Arkose 实验室集成，以帮助防范机器人攻击、帐户接管攻击和欺诈性帐户空缺。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4e5dfe26af7e1d92c20cf15e3519563e600b256
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385699"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>有关配置 Arkose 实验室与 Azure Active Directory B2C 的教程

在本教程中，了解如何将 Azure AD B2C authentication 与 Arkose 实验室集成。 Arkose 实验室可帮助组织防范机器人攻击、帐户接管攻击和欺诈性帐户空缺。  

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。

## <a name="scenario-description"></a>方案描述

下图说明了 Arkose 实验室与 Azure AD B2C 的集成方式。

![Arkose 实验室体系结构图](media/partner-arkose-labs/arkose-architecture-diagram.png)

|   |   |
|---|---|
|1     | 用户使用以前创建的帐户登录。 当用户选择 "提交" 时，会出现 "Arkose 实验强制" 的质询。 用户完成质询后，状态将发送到 Arkose 实验室以生成令牌。        |
|2     |  Arkose 实验室会将令牌发送回 Azure AD B2C。       |
|3     |  在提交登录表单之前，会将令牌发送到 Arkose 实验室进行验证。       |
|4     |  Arkose 发送回质询的成功或失败结果。       |
|5     |  如果质询成功完成，则会将登录表单提交到 Azure AD B2C，并 Azure AD B2C 完成身份验证。       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>与 Arkose 实验室集成

1. 首先联系[Arkose 实验室](https://www.arkoselabs.com/book-a-demo/)并创建帐户。

2. 创建帐户后，导航到 https://dashboard.arkoselabs.com/login 。

3. 在仪表板中，导航到 "站点设置" 查找公钥和私钥。 稍后将需要此信息来配置 Azure AD B2C。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>第1部分–创建 blob 存储以存储自定义 HTML

若要创建存储帐户，请执行以下步骤：  

1. 登录到 Azure 门户。

2. 请确保使用包含你的 Azure 订阅的目录。 在顶部菜单中选择 " **目录 + 订阅**" 筛选器，然后选择包含你的订阅的目录。 此目录与包含 Azure B2C 租户的目录不同。

3. 选择 "Azure 门户" 左上角的 "**所有服务**"，然后搜索并选择 " **存储帐户**"。

4. 选择“添加” ****。

5. 在 " **资源组**" 下，选择 " **新建**"，输入新资源组的名称，然后选择 **"确定"**。

6. 输入存储帐户的名称。 所选名称在 Azure 中需唯一，且必须为 3 到 24 个字符，并且只能包含数字和小写字母。

7. 选择存储帐户的位置或接受默认位置。

8. 接受其他所有默认值，选择 "  **查看 &" 创建**"  >  **Create**。

9. 创建存储帐户后，选择“转到资源”。

#### <a name="create-a-container"></a>创建容器

1. 在存储帐户的 "概述" 页上，选择 "  **blob**"。

2. 选择 "  **容器**"，为容器输入名称，选择 "  **blob** " （仅限 blob 的匿名读取访问），然后选择 **"确定"**。

#### <a name="enable-cross-origin-resource-sharing-cors"></a>启用跨域资源共享（CORS）

浏览器中的 Azure AD B2C 代码使用新式标准方法从用户流中指定的 URL 加载自定义内容。 CORS 允许从其他域请求网页上的受限资源。

1. 在菜单中，选择 "  **CORS**"。

2. 对于 "  **允许的来源**"，输入  `https://your-tenant-name.b2clogin.com` 。 将你的租户-name 替换为你的 Azure AD B2C 租户的名称。 例如： `https://fabrikam.b2clogin.com`。 输入租户名称时全部使用小写字母。

3. 对于 **允许的方法**，请选择 " **获取**"、" **PUT**" 和 " **选项**"。

4. 对于“允许的标头”，请输入一个星号 (*)。

5. 对于 " **公开标头**"，请输入一个星号（*）。

6. 对于“最大期限”，请输入 200。

   ![Arkose 实验室注册和登录](media/partner-arkose-labs/signup-signin-arkose.png)

7. 选择“保存”。

### <a name="part-2--set-up-a-back-end-server"></a>第2部分-设置后端服务器

下载 Git Bash 并按照以下步骤操作：

1. 按照说明[创建一个 web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php)，直到出现消息 "恭喜！已将第一个 PHP 应用部署到应用服务 "显示。

2. 打开本地文件夹，然后将该 verify-token 文件重命名为 " **verify-token.php** **"。**

3. 打开新重命名的文件 verify-token 文件和：

   a. 将内容替换为在[GitHub 存储库](https://github.com/ArkoseLabs/Azure-AD-B2C)中找到的 verify-token 文件中的内容。

   b. 将第3行 <private_key> 替换为从 Arkose 实验室仪表板获取的私钥。

4. 在本地终端窗口中，提交在 Git 中所做的更改，然后将代码更改推送到 Azure，方法是在 Git Bash 中键入以下内容：

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>第3部分–最终设置

#### <a name="store-the-custom-html"></a>存储自定义 HTML

1. 打开存储在[GitHub 存储库](https://github.com/ArkoseLabs/Azure-AD-B2C)中的 index.html 文件。

2. 将的所有实例替换 `<tenantname>` 为 b2C 租户名称（即 `<tenantname>.b2clogin.com` ）。 应有四个实例。

3. 将替换为 `<appname>` 在第2部分 "步骤 1" 中创建的应用名称。

   ![显示 Arkose 实验室 Azure CLI 的屏幕截图](media/partner-arkose-labs/arkose-azure-cli.png)

4. 将 `<public_key>` 第64行的替换为从 Arkose 实验室仪表板获取的公钥。

5. 将 index.html 文件上传到上面创建的 blob 存储。

6. 请参阅**存储**  >  **容器**  >  **上传**。

#### <a name="set-up-azure-ad-b2c"></a>设置 Azure AD B2C

> [!NOTE]
> 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。

1. 基于[此处](tutorial-create-user-flows.md)的信息创建用户流。 进入**测试用户流**部分时停止。

2. 在[用户流](user-flow-javascript-overview.md)中启用 JavaScript。

3. 在同一 "用户流" 页上，启用 "自定义页面 URL：中转到**用户流**  >  **页面布局**  >  "**使用自定义页面内容**  =  **yes**  >  **insert 自定义页面 URL**。
此自定义页面 URL 从 blob 存储中 index.html 文件的位置获取  

   ![显示 Arkose 实验室存储 url 的屏幕截图](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，然后在 "**策略**" 下选择 "**用户流**"。

2. 选择以前创建的用户流。

3. 选择 "**运行用户流**"，然后选择设置：

   a. **应用程序**-选择注册的应用（示例为 JWT）。

   b. **回复 url** -选择 "重定向 url"。

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户。

5. 注销。

6. 完成登录流。

7. 选择 "**继续**" 后，将显示 Arkose 实验室测验题。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](custom-policy-get-started.md?tabs=applications)
