---
title: Azure Data Box Gateway 设备访问、电源和连接模式
description: 介绍如何管理帮助向 Azure 传输数据的 Azure Data Box Gateway 设备的访问、电源和连接模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: c4043702bd27bb9a37fca70475ef254bbd1f7372
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561340"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>管理 Azure Data Box Gateway 的访问、电源和连接模式

本文介绍如何管理 Azure Data Box Gateway 的访问、电源和连接模式。 这些操作是通过本地 Web UI 或 Azure 门户执行的。 

在本文中，学习如何：

> [!div class="checklist"]
> * 管理对设备的访问
> * 管理连接模式
> * 管理电源

## <a name="manage-device-access"></a>管理对设备的访问

对 Data Box Gateway 设备的访问通过使用设备密码进行控制。 可以通过本地 web UI 更改密码。 你还可以在 Azure 门户中重置设备密码。

### <a name="change-device-password"></a>更改设备密码

请在本地 UI 中执行以下步骤，以更改设备密码。

1. 在本地 Web UI 中，转到“维护”>“密码更改”。****
2. 依次输入当前密码和新密码。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 确认新密码。

    ![更改密码](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. 单击“更改密码”****。
 
### <a name="reset-device-password"></a>重置设备密码

重置工作流不需要用户回忆旧密码，在密码丢失时非常有用。 在 Azure 门户中执行此工作流。

1. 在 Azure 门户中，转到“概述”>“重置管理员密码”。****

    ![重置密码](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 输入新密码并确认。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 单击“重置”。****

    ![重置密码](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理资源访问

若要创建 Azure Stack Edge/Data Box Gateway、IoT 中心和 Azure 存储资源，需要具有资源组级别的参与者或更高权限。 还需要注册相应的资源提供程序。 对于涉及激活密钥和凭据的任何操作，还需要 Azure Active Directory 图形 API 的权限。 以下部分介绍了这些内容。

### <a name="manage-microsoft-graph-api-permissions"></a>管理 Microsoft Graph API 权限

为 Azure Stack Edge 设备生成激活密钥，或执行需要凭据的任何操作时，需要具有 Microsoft Graph API 的权限。 需要凭据的操作可以是：

-  使用关联的存储帐户创建共享。
-  创建可访问设备上的共享的用户。

你应该可以`User`访问 Active Directory 租户，因为你需要能够访问它`Read all directory objects`。 你不能是来宾用户，因为他们没有权限`Read all directory objects`。 如果你是来宾，则生成激活密钥、在 Azure Stack Edge 设备上创建共享的操作都将失败，从而导致用户创建失败。

有关如何向用户提供 Microsoft Graph API 访问权限的详细信息，请参阅[Microsoft Graph 权限参考](https://docs.microsoft.com/graph/permissions-reference)。

### <a name="register-resource-providers"></a>注册资源提供程序

若要在 Azure 中预配资源（在 Azure 资源管理器模型中），需要一个支持创建该资源的资源提供程序。 例如，要设置虚拟机，订阅中应提供 "Microsoft. 计算" 资源提供程序。
 
资源提供程序在订阅级别注册。 默认情况下，任何新的 Azure 订阅都预注册到一组常用的资源提供程序中。 "DataBoxEdge" 的资源提供程序未包含在此列表中。

你无需向订阅级别授予访问权限，用户可以在其拥有所有者权限的资源组中创建类似于 "DataBoxEdge" 的资源，前提是这些资源的资源提供程序已注册。

尝试创建任何资源之前，请确保在订阅中注册了资源提供程序。 如果未注册资源提供程序，则需要确保创建新资源的用户具有足够的权限在订阅级别上注册所需的资源提供程序。 如果还没有这样做，则会看到以下错误：

*订阅\<订阅名称> 没有注册资源提供程序的权限： DataBoxEdge。*


若要获取当前订阅中已注册资源提供程序的列表，请运行以下命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

对于 Azure Stack Edge 设备， `Microsoft.DataBoxEdge`应注册。 若要`Microsoft.DataBoxEdge`注册，订阅管理员应运行以下命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

有关如何注册资源提供程序的详细信息，请参阅[解决资源提供程序注册的错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)。

## <a name="manage-connectivity-mode"></a>管理连接模式

除了在默认的正常模式下运行以外，设备还可以在部分离线或完全离线模式下运行。 下面介绍上述每种模式：

- **部分离线** – 在此模式下，设备无法将任何数据上传到共享，但可以通过 Azure 门户管理设备。

    使用按流量计费的卫星网络，并且目标是尽量减少网络带宽消耗时，通常会使用此模式。 执行设备监视操作时，仍有可能会消耗少量的网络带宽。

- **离线** – 在此模式下，设备完全与云断开连接，此时会禁用云上传和下载。 只能通过本地 Web UI 管理设备。

    想要使设备脱机时，通常会使用此模式。

若要更改设备模式，请执行以下步骤：

1. 在设备的本地 Web UI 中，转到“配置”>“云设置”。****
2. 禁用“云上传和下载”。****
3. 若要在部分离线模式下运行设备，请启用“Azure 门户管理”。****

    ![连接模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 若要在离线模式下运行设备，请禁用“Azure 门户管理”。**** 现在，只能通过本地 Web UI 管理设备。

    ![连接模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>管理电源

可使用本地 Web UI 关闭或重启虚拟设备。 在重启之前，建议使共享依次在主机和设备上脱机。 此操作可以最大程度地减少发生数据损坏的可能性。

1. 在本地 Web UI 中，转到“维护”>“电源设置”。****
2. 根据意图单击“关机”或“重启”。********

    ![电源设置](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 出现确认提示时，请单击“是”以继续****。

> [!NOTE]
> 如果关闭虚拟设备，则需要通过虚拟机监控程序管理来启动设备。
