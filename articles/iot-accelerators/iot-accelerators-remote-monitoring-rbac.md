---
title: 远程监视访问控制 - Azure | Microsoft Docs
description: 本文提供有关如何在远程监视解决方案加速器中配置基于角色的访问控制 (RBAC) 的信息
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 2774fc1374bf7fa3ed171258e8b1b51cfdb4b8b1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612939"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>在远程监视解决方案加速器中配置基于角色的访问控制

本文提供有关如何在远程监视解决方案加速器中配置基于角色的访问控制的信息。 使用基于角色的访问控制可以限制单个用户对解决方案中特定功能的访问。

## <a name="default-settings"></a>默认设置

首次部署远程监视解决方案时，有两个角色：“管理员”和“只读”。********

具有**管理员**角色的任何用户对解决方案都具有完全访问权限，包括以下权限。 具有**只读**角色的用户将仅有权查看解决方案。

| 权限            | 管理 | 只读 |
|----------------       |-------|-----------|
| 查看解决方案         | 是   | 是       |
| 更新警报         | 是   | 否        |
| 删除警报         | 是   | 否        |
| 创建设备        | 是   | 否        |
| 更新设备        | 是   | 否        |
| 删除设备        | 是   | 否        |
| 创建设备组  | 是   | 否        |
| 更新设备组  | 是   | 否        |
| 删除设备组  | 是   | 否        |
| 创建规则          | 是   | 否        |
| 更新规则          | 是   | 否        |
| 删除规则          | 是   | 否        |
| 创建作业           | 是   | 否        |
| 更新 SIM 管理 | 是   | 否        |

默认情况下，部署解决方案的用户将自动分配有**管理员**角色，并且是 Azure Active Directory 应用程序的所有者。 应用程序所有者可以通过 Azure 门户向其他用户分配角色。 如果希望另一个用户可在解决方案中分配角色，则也必须在 Azure 门户将该用户设置为应用程序所有者。

> [!NOTE]
> 只有部署解决方案的用户可以在创建解决方案后立即查看它。**** 若要向其他人授予“只读”、“管理员”或“自定义”角色访问权限以查看应用程序，请参阅下面有关添加或删除用户的说明。

## <a name="add-or-remove-users"></a>添加或删除用户

Azure Active Directory 应用程序所有者可以使用 Azure 门户在远程监视解决方案中的角色中添加或删除用户。 以下步骤使用在你部署远程监视解决方案时创建的 [Azure Active Directory 企业应用程序](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application)。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 检查[用户是否在所用的目录中](../active-directory/fundamentals/add-users-azure-active-directory.md)。 登录到 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators)站点时，已选择要使用的目录。 目录名称显示在[页面](https://www.azureiotsolutions.com/Accelerators)的右上角。

1. 在 Azure 门户中找到解决方案的“企业应用程序”。**** 在那里，通过将“应用程序类型”**** 设置为“所有应用程序”**** 来筛选列表。 按应用程序名称搜索应用程序。 应用程序名称即是远程监视解决方案的名称。 在以下屏幕截图中，解决方案和应用程序显示名称为 **contoso-rm4**。

    ![企业应用程序](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. 依次单击应用程序和“所有者”，检查你是否为应用程序的所有者。**** 在以下屏幕截图中，“Contoso 管理员”是 **contoso-rm4** 应用程序的所有者：****

    ![所有者](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    如果你不是所有者，则需要请求现有的所有者将你添加到列表。 只有所有者才能将“管理员”或“只读”等应用程序角色分配到其他用户。********

1. 若要查看应用程序中已分配到角色的用户列表，请单击“用户和组”。****

1. 若要添加用户，请单击“+ 添加用户”，然后单击“用户和组，未选择”，以便从目录中选择用户。********

1. 若要将用户分配到角色，请单击“选择角色，未选择”，然后为用户选择“管理员”或“只读”角色。************ 依次单击“选择”、“分配”********。

    ![选择角色](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. 现在，该用户可以使用该角色定义的权限访问远程监视解决方案。

1. 可以在门户中的“用户和组”页上从应用程序中删除用户。****

## <a name="create-a-custom-role"></a>创建自定义角色

首次部署远程监视解决方案时，它包含“管理员”和“只读”角色。******** 可以添加具有不同权限集的自定义角色。 若要定义自定义角色，需要：

- 在 Azure 门户中将新角色添加到应用程序。
- 为身份验证和授权微服务中的新角色定义策略。
- 更新解决方案的 Web UI。

### <a name="define-a-custom-role-in-the-azure-portal"></a>在 Azure 门户中定义自定义角色

以下步骤说明如何将角色添加到 Azure Active Directory 中的应用程序。 此示例创建一个新角色，使成员能够在远程监视解决方案中创建、更新和删除设备。

1. 在 Azure 门户中找到解决方案的“应用注册”。**** 应用程序名称即是远程监视解决方案的名称。 在以下屏幕截图中，解决方案和应用程序显示名称为 **contoso-rm4**。

    ![应用注册](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. 选择应用程序，然后单击“清单”。**** 可以看到，为应用程序定义了两个现有的[应用角色](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles)：

    ![查看清单](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. 按以下代码片段中所示编辑清单，以添加名为 **ManageDevices** 的角色。 需要为新角色 ID 指定一个唯一的字符串，例如 GUID。 可以使用 [Online GUID Generator](https://www.guidgenerator.com/) 等服务生成新 GUID：

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    保存更改。

### <a name="define-a-policy-for-the-new-role"></a>为新角色定义策略

若要在 Azure 门户中将角色添加到应用，需在 [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) 中，为分配所需设备管理权限的角色定义一个策略。

1. 将[远程监视微服务](https://github.com/Azure/remote-monitoring-services-dotnet)存储库从 GitHub 克隆到本地计算机。

1. 按以下代码片段中所示编辑 Sauth/Services/data/policies/roles.json 文件，为 ManageDevices 角色添加策略********。 **ID** 和 **Role** 值必须与前一部分所述应用清单中的角色定义相匹配。 充当 **ManageDevices** 角色的人员可以使用允许的操作列表来创建、更新设备，以及删除已连接到解决方案的设备：

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. 编辑完 **Services/data/policies/roles.json** 文件后，请重新生成身份验证和授权微服务，并将其重新部署到解决方案加速器。

### <a name="how-the-web-ui-enforces-permissions"></a>Web UI 如何强制实施权限

Web UI 使用[身份验证和授权微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)来确定用户可执行的操作，以及要在 UI 中显示的控件。 例如，如果解决方案名为 **contoso-rm4**，则 Web UI 将通过发送以下请求来检索当前用户可执行的操作列表：

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

对于充当 **ManageDevices** 角色、名为“设备管理员”的用户，响应正文包含以下 JSON：****

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

在 [Web UI](https://github.com/Azure/pcs-remote-monitoring-webui/) 中，摘自 [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) 的以下代码片段显示如何以声明方式强制实施权限：

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

有关详细信息，请参阅[受保护的组件](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md)。 可在 [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) 文件中定义其他权限。

### <a name="how-the-microservices-enforce-permissions"></a>微服务如何强制实施权限

微服务也会检查权限，以防止未经授权的 API 请求。 当微服务收到 API 请求时，会解码并验证 JWT 令牌，以获取与用户角色相关联的用户 ID 和权限。

在 [IoT 中心管理器微服务](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)中，摘自 [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) 文件的以下代码片段显示如何强制实施权限：

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>后续步骤

本文已介绍如何在远程监视解决方案加速器中实现基于角色的访问控制。

请参阅[配置时序见解资源管理器的访问控制](iot-accelerators-remote-monitoring-rbac-tsi.md)，了解有关如何在远程监视解决方案加速器中管理时序见解资源管理器的访问权限信息。

有关远程监视解决方案加速器的更多概念信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)

有关自定义远程监视解决方案的详细信息，请参阅[自定义和重新部署微服务](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->