---
title: Azure AD 身份验证入门
description: 了解如何访问 Azure Active Directory (Azure AD) 身份验证来使用 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00808c25ac84da852cce6169fb210767ee2b56cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265875"
---
# <a name="get-credentials-to-access-media-services-api"></a>获取访问媒体服务 API 的凭据

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

通过 Azure AD 身份验证访问 Azure 媒体服务 API 时，可使用下面两种身份验证选项：

- **服务主体身份验证**（推荐）

    验证服务。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用程序：Web 应用程序、函数应用程序、逻辑应用程序、API 或微服务。
- **用户身份验证**

    验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应先提示用户输入凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 

本文介绍了获取访问媒体服务 API 的凭据的步骤。 有以下选项卡可供选择。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](create-account-howto.md)。

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>API 访问

通过“API 访问”页面，可选择要用来连接 API 的身份验证方法。 该页面还提供了连接到 API 所需的值。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择媒体服务帐户。
2. 选择左侧导航栏上的“API 访问(新)”边栏选项卡。
3. 在“连接到媒体服务 API”下，选择要连接到的媒体服务 API 版本（该服务的最新版本是 V3）。

### <a name="service-principal-authentication--recommended"></a>服务主体身份验证（推荐）

使用 Azure Active Directory (Azure AD) 应用和密钥对服务进行身份验证。 建议将它用于对媒体服务 API 的任何中间层服务调用， 例如 Web 应用、Functions、逻辑应用、API 和微服务。 这是推荐的身份验证方法。

#### <a name="manage-your-azure-ad-app-and-secret"></a>管理 Azure AD 应用和密钥

通过“理 AAD 应用和密钥”部分，可选择或创建新的 Azure AD 应用并生成密钥。 出于安全原因，边栏选项卡关闭后密钥无法显示。 应用程序使用应用程序 ID 和密钥进行验证，以便为媒体服务获取有效的令牌。

请确保你拥有足够的权限，可向 Azure AD 租户注册应用程序，并将应用程序分配给 Azure 订阅中的角色。 有关详细信息，请参阅[所需的权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

#### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

在“连接到媒体服务 API”页面，可查看用于连接服务主体应用程序的值。 可获取文本值，也可复制 JSON 或 XML 块。

### <a name="user-authentication"></a>用户身份验证

此选项可用于对使用应用与媒体服务资源进行交互的 Azure Active Directory 的员工或成员进行身份验证。 交互式应用程序应先提示用户输入用户凭据。 这种身份验证方法应仅用于管理应用程序。

#### <a name="connect-to-media-services-api"></a>连接到媒体服务 API

从“连接到媒体服务 API”部分复制凭据来连接用户应用程序。 可获取文本值，也可复制 JSON 或 XML 块。

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>后续步骤

[教程：使用媒体服务 v3 上传编码和流式传输视频](stream-files-tutorial-with-api.md)。
