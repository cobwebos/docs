---
title: 在 Azure AD 中注册服务应用 - Azure API for FHIR
description: 了解如何在 Azure Active Directory 中注册可用于身份验证和获取令牌的服务客户端应用程序。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871077"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册服务客户端应用程序

本文介绍如何在 Azure Active Directory 中注册服务客户端应用程序。 客户端应用程序注册是可用于身份验证和获取令牌的应用程序的 Azure Active Directory 表示形式。 服务客户端旨在供应用程序用来获取访问令牌，而无需用户进行交互式身份验证。 服务客户端拥有特定的应用程序权限，在获取访问令牌时使用应用程序机密（密码）。

遵循以下步骤创建新的服务客户端。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册”：  

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击“新建注册”  。

## <a name="service-client-application-details"></a>服务客户端应用程序详细信息

* 服务客户端需要一个显示名称，你也可以提供回复 URL，但通常不会使用回复 URL。

    ![Azure 门户。 新建服务客户端应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API 权限

需要授予服务客户端应用程序角色。 

1. 打开“API 权限”，选择你的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。 如果使用 Azure API for FHIR，可以通过搜索“我的组织使用的 API”下的 Azure 医疗保健 API，来添加对 Azure 医疗保健 API 的权限。 

    ![Azure 门户。 服务客户端 API 权限](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. 从资源应用程序上定义的应用程序角色中选择角色：

    ![Azure 门户。 服务客户端应用程序权限](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. 向应用程序授予许可。 如果你没有所需的权限，请咨询 Azure Active Directory 管理员：

    ![Azure 门户。 服务客户端管理员许可](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>应用程序机密

服务客户端需要机密（密码），获取令牌时将使用该机密。

1. 单击“证书和机密”

2. 单击“新建客户端机密” 

    ![Azure 门户。 服务客户端机密](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 提供机密的持续时间。

4. 生成机密后，它只会在门户中显示一次。 请记下该机密并存储在安全的位置。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册服务客户端应用程序。 接下来请在 Azure 中部署 FHIR API。
 
>[!div class="nextstepaction"]
>[部署开源 FHIR 服务器](fhir-oss-powershell-quickstart.md)