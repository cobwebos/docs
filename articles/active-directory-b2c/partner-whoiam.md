---
title: 通过 WhoIAM 配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 在本教程中，了解如何将 Azure AD B2C authentication 与 WhoIAM 集成以进行用户验证。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 718ccbaa57ffe9f4ebaf4e8df448b602ba8cc3fa
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89293143"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>有关配置 WhoIAM 与 Azure Active Directory B2C 的教程

在此示例教程中，我们提供了有关如何在你的环境中配置 [WhoIAM](https://www.whoiam.ai/brims/) 品牌标识管理系统 (BRIMS) 的指导，以及如何将其与 Active Directory B2C (Azure AD B2C) 集成。

BRIMS 是一组在你的环境中部署的应用和服务。 它为用户群提供语音、短信和电子邮件验证。 BRIMS 与你的现有标识和访问管理解决方案结合使用，与平台无关。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

- WhoIAM [试用帐户](https://www.whoiam.ai/contact-us/)。

## <a name="scenario-description"></a>方案描述

WhoIAM 集成包括以下组件：

- Azure AD B2C 租户。 这是授权服务器，它基于其中定义的自定义策略来验证用户的凭据。 它也称为标识提供者。

- 用于管理客户端及其配置的管理门户。

- 通过终结点公开各种功能的 API 服务。  

- Azure Cosmos DB，它充当 BRIMS 管理门户和 API 服务的后端。

下面的体系结构关系图显示了实现。

![与 WhoIAM 集成 Azure AD B2C 的体系结构示意图。](media/partner-whoiam/whoiam-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户会进入一个页面，用于开始向使用 Azure AD B2C 作为标识提供者的应用的注册或登录请求。
| 2. | 作为身份验证的一部分，用户请求验证其电子邮件或电话的所有权，或者使用其语音作为生物识别验证因素。  
| 3. | Azure AD B2C 调用 BRIMS API 服务，并传入用户的电子邮件地址、电话号码和语音录制。
| 4. | BRIMS 使用预定义的电子邮件和短信等预定义配置，以与应用的样式一致的方式与用户交互。
| 5. | 用户的标识验证完成后，BRIMS 将返回 Azure AD B2C 的令牌，以指示验证的结果。 然后 Azure AD B2C 向用户授予对应用程序的访问权限，否则将无法进行身份验证尝试。  

## <a name="sign-up-with-whoiam"></a>注册 WhoIAM

1. 请联系 [WhoIAM](https://www.whoiam.ai/contact-us/) 并创建一个 BRIMS 帐户。

2. 使用可用的注册准则，并配置以下 Azure 服务：

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：用于安全存储密码，如邮件服务密码。

    - [Azure App Service](https://azure.microsoft.com/services/app-service/)：用于托管 BRIMS API 和管理门户服务。

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)：用于对管理门户的管理用户进行身份验证。

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：用于存储和检索设置。

    -  (可选) [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications) ：用于登录 API 和管理门户。

3. 在你的 Azure 环境中部署 BRIMS API 和 BRIMS 管理门户。

4. BRIMS 注册文档中提供了 Azure AD B2C 自定义策略示例。 按照文档配置应用，并将 BRIMS 平台用于用户身份验证。  

有关 WhoIAM 的 BRIMS 的详细信息，请参阅 [产品文档](https://www.whoiam.ai/brims/)。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户。 在“策略”下，选择“Identity Experience Framework”。 

2. 选择先前创建的 **SignUpSignIn**。

3. 选择 " **运行用户流** "，然后：

   a. 对于 " **应用程序**"，选择已注册的应用 (示例为 JWT) 。

   b. 对于 " **答复 url**"，选择 " **重定向 url**"。

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户。

5. 创建用户属性后，将在流中调用 BRIMS 服务。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
