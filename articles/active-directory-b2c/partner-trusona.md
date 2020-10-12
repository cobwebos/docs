---
title: Trusona 和 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何在 Azure AD B2C 上将 Trusona 添加为标识提供程序以启用无密码 authentication。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0d5b369e1c143b3df4157329bcf7d3a3f7142d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87489463"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>将 Trusona 与 Azure Active Directory B2C 集成

Trusona 是独立的软件供应商 (ISV) 提供商，可通过启用无密码 authentication、多重身份验证和数字许可扫描来帮助保护登录。 本文介绍如何在 Azure AD B2C 中将 Trusona 添加为标识提供程序，以启用无密码 authentication。

## <a name="prerequisites"></a>必备条件

若要开始，你将需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。
* Trusona 上的[试用帐户](https://www.trusona.com/aadb2c)

## <a name="scenario-description"></a>方案描述

在这种情况下，Trusona 充当标识提供程序，以便 Azure AD B2C 启用无密码 authentication。 以下组件组成解决方案：

* Azure AD B2C 组合登录和注册策略
* Trusona 作为标识提供程序添加到 Azure AD B2C
* 可下载的 Trusona 应用

![Trusona 体系结构图](media/partner-trusona/trusona-architecture-diagram.png)

| 步骤 | 说明 |
|------|------|
|1     | 用户尝试登录或注册应用程序。 用户通过 Azure AD B2C 注册和登录策略进行身份验证。 注册过程中，将使用用户以前在 Trusona 应用中验证的电子邮件地址。     |
|2     | Azure B2C 使用隐式流将用户重定向到 Trusona OpenID Connect (OIDC) 标识提供者。     |
|3     | 对于基于桌面 PC 的登录，Trusona 显示唯一的无状态、动态和动态 QR 码，以便使用 Trusona 应用进行扫描。 对于基于移动的登录名，Trusona 使用 "深层链接" 打开 Trusona 应用。 这两种方法用于设备，最终用户发现。     |
|4     | 用户通过 Trusona 应用扫描显示的 QR 码。     |
|5     | 用户的帐户位于 Trusona 云服务中，并已准备好身份验证。     |
|6     | Trusona 云服务通过发送到 Trusona 应用的推送通知向用户发出身份验证质询：<br>a. 系统将提示用户提供身份验证质询。 <br> b. 用户选择接受或拒绝质询。 <br> c. 要求用户使用 OS security (例如，生物识别、密码、PIN 或模式) ，通过安全 Enclave/受信任执行环境中的私钥来确认和签署质询。 <br> d. Trusona 应用程序会实时基于身份验证的参数生成动态抗重播有效负载。 <br> e. 整个响应 (通过安全 Enclave/受信任执行环境中的私钥) ，并返回到 Trusona 云服务进行验证。      |
|7     |  Trusona 云服务将用户重定向回使用 id_token 的启动应用程序。 Azure AD B2C 使用标识提供程序安装过程中配置的 Trusona 的已发布 OpenID 配置验证 id_token。    |
|  |  |

## <a name="onboard-with-trusona"></a>内置 Trusona

1. 填写 [表单](https://www.trusona.com/aadb2c) 以创建 Trusona 帐户并开始使用。

2. 从应用商店下载 Trusona 移动应用。 安装应用并注册电子邮件。

3. 通过该软件发送的安全 "魔术链接" 验证你的电子邮件。  

4. 请参阅 [Trusona 开发人员](https://dashboard.trusona.com) 的自助服务仪表板。

5. 选择 " **我已准备就绪** " 并向你的 Trusona 应用程序进行身份验证。

6. 从左侧导航面板中，选择 " **OIDC 集成**"。

7. 选择 " **创建 OpenID Connect 集成**"。

8. 提供你选择的 **名称** ，并使用之前提供的域信息 (例如，Contoso) **客户端重定向主机 "字段**中。  

   > [!NOTE]
   > Azure Active Directory 的初始域名用作客户端重定向主机。

9. 按照 [Trusona 集成指南](https://docs.trusona.com/integrations/aad-b2c-integration/)中的说明进行操作。 出现提示时，使用初始域名 (例如，Contoso) 在上一步中引用。  

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="add-a-new-identity-provider"></a>添加新的标识提供者

> [!NOTE]
> 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。

2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。

4. 导航到 "**仪表板**"  >  **Azure Active Directory B2C**  >  **标识提供者**。

3. 选择 " **标识提供者**"。

4. 选择“添加”  。

### <a name="configure-an-identity-provider"></a>配置标识提供者  

1. 选择 "**标识提供者类型**  >  **OpenID 连接 (预览") **。

2. 填写表单以设置标识提供者：  

   | 属性 | 值  |
   | :--- | :--- |
   | 元数据 URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | 客户端 ID | 将通过电子邮件发送给你的 Trusona |
   | 范围 | OpenID 配置文件电子邮件 |
   | 响应类型 | Id_token |
   | 响应模式  | Form_post |

3. 选择“确定”  。  

4. 选择 " **映射此标识提供者的声明**"。  

5. 填写表单以映射标识提供者：

   | 属性 | 值  |
   | :--- | :--- |
   | UserID | Sub  |
   | 显示名称 | nickname |
   | 给定名称 | given_name |
   | Surname | Family_name |
   | 响应模式 | 电子邮件 |

6. 选择 **"确定"** 完成新 OIDC 标识提供程序的设置。

### <a name="create-a-user-flow-policy"></a>创建用户流策略

现在，应会看到 Trusona 作为 B2C 标识提供者中列出的 **新 OpenID Connect 标识提供者** 。

1. 在 Azure AD B2C 租户中的 " **策略**" 下，选择 " **用户流**"。

1. 选择 " **新建用户流**"。

1. 选择 " **注册并登录**"，选择一个版本，然后选择 " **创建**"。

1. 输入策略的 **名称** 。

1. 在 " **标识提供者** " 部分中，选择新创建的 **Trusona 标识提供者**。

   > [!NOTE]
   > 由于 Trusona 本质上是多重身份验证，因此最好禁用多重身份验证。

1. 选择“创建”  。

1. 在 " **用户属性和声明**" 下，选择 " **显示更多**"。 在窗体中，选择在前面部分的标识提供程序安装过程中指定的至少一个属性。

1. 选择“确定”  。  

### <a name="test-the-policy"></a>测试策略

1. 选择新创建的策略。

2. 选择“运行用户流”。

3. 在窗体中，输入答复 URL。

4. 选择“运行用户流”。 应重定向到 Trusona OIDC 网关。 在 Trusona gateway 上，使用 Trusona 应用或使用 Trusona mobile SDK 的自定义应用扫描显示的安全 QR 码。

5. 在扫描安全 QR 码后，应将你重定向到在步骤3中定义的回复 URL。

## <a name="next-steps"></a>后续步骤  

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

- [AAD B2C 中的自定义策略入门](custom-policy-get-started.md?tabs=applications)
