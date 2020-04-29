---
title: Azure AD 身份验证入门
description: 了解如何访问 Azure Active Directory (Azure AD) 身份验证以使用 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79478248"
---
# <a name="get-credentials-to-access-media-services-api"></a>获取用于访问媒体服务 API 的凭据  

使用 Azure AD 身份验证访问 Azure 媒体服务 API 时，有两个身份验证选项可供选择：

- **服务主体身份验证**（推荐）

    对服务进行身份验证。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用：Web 应用、函数应用、逻辑应用、API 或微服务。
- **用户身份验证**

    验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应首先提示用户输入凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 

本文介绍获取用于访问媒体服务 API 的凭据的步骤。 从以下选项卡中进行选择。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](create-account-howto.md)。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

### <a name="api-access"></a>API 访问 

在“API 访问”页中，可以选择用于连接 API 的身份验证方法。  此页还提供连接到 API 所需的值。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择媒体服务帐户。
2. 选择与媒体服务 API 的连接方式。
3. 在“连接到媒体服务 API”下，选择要连接到的媒体服务 API 版本（V3 是最新服务版本）。 

### <a name="service-principal-authentication--recommended"></a>服务主体身份验证（推荐）

使用 Azure Active Directory (Azure AD) 应用和机密对服务进行身份验证。 建议对调用媒体服务 API 的所有中间层服务执行此操作。 例如，Web 应用、Functions、逻辑应用、API 和微服务。 这是推荐使用的身份验证方法。

#### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 应用和机密

在“管理 AAD 应用和机密”部分，可以选择或新建 Azure AD 应用并生成机密。  出于安全方面的原因，关闭边栏选项卡后，无法显示机密。 应用程序使用应用程序 ID 和机密进行身份验证，以获取媒体服务的有效令牌。

务必拥有足够的权限，以便向 Azure AD 租户注册应用程序，并将应用程序分配给 Azure 订阅中的角色。 有关详细信息，请参阅[所需权限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

#### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

“连接到媒体服务 API”提供用于连接服务主体应用程序的值。  可以获取文本值，或者复制 JSON 或 XML 块。

### <a name="user-authentication"></a>用户身份验证

此选项可用于对某个使用应用来与媒体服务资源交互的 Azure Active Directory 员工或成员进行身份验证。 交互式应用程序应先提示用户输入用户凭据。 此身份验证方法只可用于管理型应用程序。

#### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

从“连接到媒体服务 API”部分复制用于连接用户应用程序的凭据  。 可以获取文本值，或者复制 JSON 或 XML 块。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>后续步骤

[教程：使用媒体服务 v3 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)。
