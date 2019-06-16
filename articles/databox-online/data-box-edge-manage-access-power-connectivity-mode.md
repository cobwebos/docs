---
title: Azure 数据框边缘设备的访问、 电源和连接模式 |Microsoft Docs
description: 介绍如何管理访问、 电源和连接模式可帮助将数据传输到 Azure 的 Azure 数据框边缘设备
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476901"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>在 Azure 数据框 edge 管理访问、 电源和连接模式

本文介绍如何在 Azure 数据框边缘为管理访问、 电源和连接模式。 这些操作是通过本地 Web UI 或 Azure 门户执行的。

在本文中，学习如何：

> [!div class="checklist"]
> * 管理对设备的访问
> * 管理连接模式
> * 管理电源


## <a name="manage-device-access"></a>管理对设备的访问

向你的数据框边缘设备的访问控制通过使用设备密码。 你可以通过本地 web UI 密码。 此外可以重置设备密码在 Azure 门户中。

### <a name="change-device-password"></a>更改设备密码

请按照下列步骤在本地 UI 更改设备密码。

1. 在本地 Web UI 中，转到“维护”>“密码更改”。 
2. 依次输入当前密码和新密码。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 确认新密码。

    ![更改密码](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 选择“更改密码”  。
 
### <a name="reset-device-password"></a>重置设备密码

重置工作流不需要用户回忆旧密码，在密码丢失时非常有用。 在 Azure 门户中执行此工作流。

1. 在 Azure 门户中，转到“概述”>“重置管理员密码”。 

    ![重置密码](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 输入新密码并确认。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 选择**重置**。

    ![重置密码](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理资源的访问权限

若要创建数据框边缘/数据框网关、 IoT 中心和 Azure 存储资源，您需要在资源组级别权限作为参与者或更高版本。 您还需要相应的资源提供程序注册。 对于涉及激活密钥和凭据的任何操作，则还要求向 Azure Active Directory 图形 API 的权限。 以下各节中描述了这些。

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>管理 Microsoft Azure Active Directory 图形 API 权限

在生成的数据框边缘设备，或执行任何操作，需要提供凭据的激活密钥，您需要对 Azure Active Directory 图形 API 的权限。 可能需要凭据的操作：

-  创建具有关联的存储帐户的共享。
-  创建用户有权访问设备上的共享。

您应该`User`因为您需要能够访问 Active Directory 租户上`Read all directory objects`。 不能为来宾用户，因为没有权限`Read all directory objects`。 如果你是来宾，如生成的激活密钥，创建共享数据框边缘设备上的操作创建的用户将所有失败。

有关如何向 Azure Active Directory 图形 API 的用户提供访问权限的详细信息，请参阅[管理员、 用户和来宾用户的默认访问](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。

### <a name="register-resource-providers"></a>注册资源提供程序

若要预配 Azure 中的资源 （在 Azure 资源管理器模型中），您需要支持该资源的创建的资源提供程序。 例如，若要预配虚拟机，应具有 Microsoft.Compute 资源提供程序订阅中可用。
 
资源提供程序的订阅级别上注册。 默认情况下，任何新的 Azure 订阅是预注册的常用的资源提供程序的列表。 此列表中不包含 Microsoft.DataBoxEdge 的资源提供程序。

无需授予用户能够创建 Microsoft.DataBoxEdge 等他们拥有所有者权限，其资源组中的资源，只要这些资源的资源提供程序已对订阅级别访问权限注册。

尝试创建的任何资源之前，请确保在订阅中注册资源提供程序。 如果未注册资源提供程序，你将需要确保创建新资源的用户具有足够的权限注册的订阅级别上的所需的资源提供程序。 如果尚未执行此同时，您将看到以下错误：

*订阅<Subscription name>不具有权限，无法注册资源提供程序：Microsoft.DataBoxEdge.*


若要获取当前订阅中已注册的资源提供程序的列表，请运行以下命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

为数据框边缘设备`Microsoft.DataBoxEdge`应注册。 若要注册`Microsoft.DataBoxEdge`，订阅管理员可以运行以下命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

有关如何注册资源提供程序的详细信息，请参阅[解决资源提供程序注册错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)。

## <a name="manage-connectivity-mode"></a>管理连接模式

除了默认完全连接模式下，还可以在部分连接，或完全断开连接模式下运行你的设备。 下面介绍上述每种模式：

- **完全连接**-这是正常的默认模式在设备上进行操作。 在此模式下启用的云上传和下载数据。 可以使用 Azure 门户或本地 web UI 来管理设备。

- **部分已断开连接**– 该设备不能在此模式下上, 传或下载但是可以通过 Azure 门户中管理数据的任何共享。

    使用按流量计费的卫星网络，并且目标是尽量减少网络带宽消耗时，通常会使用此模式。 执行设备监视操作时，仍有可能会消耗少量的网络带宽。

- **离线** – 在此模式下，设备完全与云断开连接，此时会禁用云上传和下载。 只能通过本地 Web UI 管理设备。

    想要使设备脱机时，通常会使用此模式。

若要更改设备模式，请执行以下步骤：

1. 在设备的本地 Web UI 中，转到“配置”>“云设置”。 
2. 从下拉列表中，选择你想要运行中的设备的模式。 可以选择从**完全连接**，**已部分连接**，并**完全断开连接**。 若要在部分离线模式下运行设备，请启用“Azure 门户管理”。 

    ![连接模式](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理电源

可以关闭或重启物理设备使用本地 web UI。 我们建议您重新启动之前，使共享脱机数据服务器，然后在设备上的。 此操作可以最大程度地减少发生数据损坏的可能性。

1. 在本地 Web UI 中，转到“维护”>“电源设置”。 
2. 选择**关机**或**重新启动**具体取决于你想要执行操作。

    ![电源设置](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 当系统提示确认，则选择**是**以继续。

> [!NOTE]
> 如果你关闭物理设备，需要将其打开设备上按电源按钮。

## <a name="next-steps"></a>后续步骤

- 了解如何[管理共享](data-box-edge-manage-shares.md)。