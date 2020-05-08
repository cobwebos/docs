---
title: Azure Stack 边缘设备访问、电源和连接模式 |Microsoft Docs
description: 描述如何管理可帮助将数据传输到 Azure 的 Azure Stack Edge 设备的访问、电源和连接模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 939296b1cf606401a801dd72eccbad23da766018
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569610"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge"></a>为 Azure Stack 边缘管理访问、电源和连接模式

本文介绍如何管理 Azure Stack 边缘的访问、电源和连接模式。 这些操作是通过本地 Web UI 或 Azure 门户执行的。

在本文中，学习如何：

> [!div class="checklist"]
> * 管理对设备的访问
> * 管理连接模式
> * 管理电源


## <a name="manage-device-access"></a>管理对设备的访问

对 Azure Stack Edge 设备的访问通过使用设备密码进行控制。 可以通过本地 web UI 更改密码。 你还可以在 Azure 门户中重置设备密码。

### <a name="change-device-password"></a>更改设备密码

请在本地 UI 中执行以下步骤，以更改设备密码。

1. 在本地 Web UI 中，转到“维护”>“密码更改”。****
2. 依次输入当前密码和新密码。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 确认新密码。

    ![更改密码](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 选择“更改密码”****。
 
### <a name="reset-device-password"></a>重置设备密码

重置工作流不需要用户回忆旧密码，在密码丢失时非常有用。 在 Azure 门户中执行此工作流。

1. 在 Azure 门户中，转到“概述”>“重置管理员密码”。****

    ![重置密码](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 输入新密码并确认。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 选择“重置”****。

    ![重置密码](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理资源访问

若要创建 Azure Stack Edge/Data Box Gateway、IoT 中心和 Azure 存储资源，需要具有资源组级别的参与者或更高权限。 还需要注册相应的资源提供程序。 对于涉及激活密钥和凭据的任何操作，还需要对 Microsoft Graph API 的权限。 以下部分介绍了这些内容。 

### <a name="manage-microsoft-graph-api-permissions"></a>管理 Microsoft Graph API 权限

为 Azure Stack Edge 设备生成激活密钥，或执行需要凭据的任何操作时，需要具有 Azure Active Directory 图形 API 的权限。 需要凭据的操作可以是：

-  使用关联的存储帐户创建共享。
-  创建可访问设备上的共享的用户。

你应该可以`User`访问 Active Directory 租户，因为你需要能够访问它`Read all directory objects`。 你不能是来宾用户，因为他们没有权限`Read all directory objects`。 如果你是来宾，则生成激活密钥、创建 Azure Stack 边缘设备上的共享、创建用户、配置边缘计算角色、重置设备密码的操作都将失败。

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

除了默认的完全连接模式，你的设备还可以在部分连接或完全断开连接模式下运行。 下面介绍上述每种模式：

- **完全连接**-这是设备运行时的常规默认模式。 在此模式下，云上传和下载数据都处于启用状态。 你可以使用 Azure 门户或本地 web UI 来管理设备。

- **部分断开**–在此模式下，设备无法上载或下载任何共享数据，但可通过 Azure 门户进行管理。

    使用按流量计费的卫星网络，并且目标是尽量减少网络带宽消耗时，通常会使用此模式。 执行设备监视操作时，仍有可能会消耗少量的网络带宽。

- **离线** – 在此模式下，设备完全与云断开连接，此时会禁用云上传和下载。 只能通过本地 Web UI 管理设备。

    想要使设备脱机时，通常会使用此模式。

若要更改设备模式，请执行以下步骤：

1. 在设备的本地 Web UI 中，转到“配置”>“云设置”。****
2. 从下拉列表中，选择要在其中操作设备的模式。 你可以选择 "**完全连接**"、"**部分连接**" 和 "**完全断开连接**"。 若要在部分离线模式下运行设备，请启用“Azure 门户管理”。****

    ![连接模式](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理电源

你可以使用本地 web UI 关闭或重新启动你的物理设备。 建议在重新启动之前，先在数据服务器和设备上使共享脱机。 此操作可以最大程度地减少发生数据损坏的可能性。

1. 在本地 Web UI 中，转到“维护”>“电源设置”。****
2. 根据要执行的操作，选择 "**关机**" 或 "**重新启动**"。

    ![电源设置](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 出现确认提示时，请选择 **"是"** 以继续。

> [!NOTE]
> 如果关闭物理设备，则需要按设备上的电源按钮将其打开。

## <a name="next-steps"></a>后续步骤

- 了解如何[管理共享](azure-stack-edge-manage-shares.md)。
