---
title: 如何通过 Azure AD 和 Azure Maps REST Api 保护输入受限设备
titleSuffix: Azure Maps
description: 如何配置无浏览器的应用程序，该应用程序支持登录到 Azure AD 并调用 Azure Maps REST Api。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 91d73ad14cac77e4b00e90ec11791ef141436b7e
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126732"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>使用 Azure AD 和 Azure Maps REST Api 保护输入受限设备

本指南讨论如何保护不能安全地存储机密或接受浏览器输入的公共应用程序或设备。 这些类型的应用程序位于 IoT 或物联网的类别下。 这些应用程序的一些示例可能包括：智能电视设备或传感器数据发出的应用程序。 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>在 Azure AD 中创建应用程序注册

> [!NOTE]
> * **先决条件阅读：** [方案：调用 Web api 的桌面应用](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview)
> * 以下方案使用的设备代码流不涉及 web 浏览器来获取令牌。

在 Azure AD 中创建基于设备的应用程序，以启用 Azure AD 登录。 将向此应用程序授予 Azure Maps REST Api 的访问权限。

1. 在 "Azure 门户的" Azure 服务 "列表中，选择" **Azure Active Directory**"  >  **应用注册**"  >  **新注册**"。  

    > [!div class="mx-imgBorder"]
    > ![应用注册](./media/how-to-manage-authentication/app-registration.png)

2. 输入一个**名称**，**在此组织目录中选择仅**作为**支持的帐户类型**的帐户。 在 "**重定向 uri**" 中，指定**公共客户端/本机（移动 & 桌面）** ，并将添加 `https://login.microsoftonline.com/common/oauth2/nativeclient` 到值。 有关更多详细信息，请参阅 Azure AD[桌面应用程序调用 Web api：应用注册](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)。 然后**注册**应用程序。

    > [!div class="mx-imgBorder"]
    > ![为名称和重定向 uri 添加应用注册详细信息](./media/azure-maps-authentication/devicecode-app-registration.png)

3. 导航到 "**身份验证**"，并启用 "**将应用程序视为公共客户端**"。 这将启用 Azure AD 的设备代码身份验证。
    
    > [!div class="mx-imgBorder"]
    > ![启用作为公用客户端的应用注册](./media/azure-maps-authentication/devicecode-public-client.png)

4.  若要将委派的 API 权限分配到 Azure Maps，请访问应用程序。 然后选择 " **API 权限**" "  >  **添加权限**"。 在 "**我的组织使用的 api**" 下，搜索并选择 " **Azure Maps**"。

    > [!div class="mx-imgBorder"]
    > ![添加应用 API 权限](./media/how-to-manage-authentication/app-permissions.png)

5. 选中 " **Access Azure Maps**旁边的复选框，然后选择"**添加权限**"。

    > [!div class="mx-imgBorder"]
    > ![选择应用 API 权限](./media/how-to-manage-authentication/select-app-permissions.png)

6. 为用户或组配置基于 Azure 角色的访问控制。 请参阅[向用户授予用于 Azure Maps 的基于角色的访问权限](#grant-role-based-access-for-users-to-azure-maps)。

7. 添加用于在应用程序中获取令牌流的代码。有关实现的详细信息，请参阅[设备代码流](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-acquire-token#device-code-flow)。 获取令牌时，引用作用域： `user_impersonation` 在前面的步骤中选择的作用域。

> [!Tip]
> 使用 Microsoft 身份验证库（MSAL）获取访问令牌。 请参阅[用于调用 Web api 的桌面应用的建议：代码配置](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-configuration)

8. 使用从 Azure AD 获取的令牌编写 HTTP 请求，并使用有效的 HTTP 客户端发送请求。

### <a name="sample-request"></a>示例请求
下面是一个示例请求正文，用于上传用中心点和半径表示为圆形几何的简单地域隔离区内。

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 下面的示例请求正文在 GeoJSON 中：
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>示例响应：

标头：
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

正文：
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>后续步骤

查找 Azure Maps 帐户的 API 使用情况指标：
> [!div class="nextstepaction"]
> [查看使用情况指标](how-to-view-api-usage.md)
