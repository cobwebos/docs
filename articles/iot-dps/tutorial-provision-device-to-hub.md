---
title: "使用 Azure IoT 中心设备预配服务预配设备 | Microsoft Docs"
description: "使用 Azure IoT 中心设备预配服务将设备预配到单个 IoT 中心"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.contentlocale: zh-cn
ms.lasthandoff: 09/06/2017

---

# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心设备预配服务将设备预配到 IoT 中心

前面的教程介绍了设置设备以连接到设备预配服务的方法。 本教程将介绍如何通过此服务使用“注册列表”将设备预配到单一 IoT 中心。 本教程演示如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

## <a name="prerequisites"></a>先决条件

继续之前，请确保已配置设备及其“硬件安全模块”（按[使用 Azure IoT 中心设备预配服务设置设备以进行预配](./tutorial-set-up-device.md)教程所述）。


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>注册设备

此步骤需要将设备的唯一安全项目添加到设备预配服务。 这些安全项目如下所示：

- 对于基于 TPM 的设备：
    - 对每个 TPM 芯片或模拟唯一的“认可密钥”。 请阅读[了解 TPM 认可密钥](https://technet.microsoft.com/library/cc770443.aspx)获取详细信息。
    - 注册 ID，用于在命名空间/作用域内唯一标识设备。 可能与设备 ID 相同或不同。 此 ID 是每台设备的必备项。 对于基于 TPM 的设备，可能从 TPM 本身派生注册 ID，例如 TPM 认可密钥的 SHA-256 哈希。

    ![门户中有关 TPM 的注册信息](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- 对于基于 X.509 的设备：
    - [颁发给 X.509（芯片或模拟）的证书](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) 采用 .pem 或 .cer 文件的格式。 对于单独注册，需要对 X.509 系统使用“签名人证书”；而对于注册组，则需要使用“根证书”。

    ![门户中有关 X.509 的注册信息](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


可通过两种方法向设备预配服务注册设备：

- **注册组** 表示共享特定证明机制的一组设备。 对于共享所需初始配置的大量设备，或者全部转到同一租户的设备，建议使用注册组。

    ![门户中 X.509 的注册组](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **单独注册** 表示可使用设备预配服务进行注册的单一设备条目。 单独注册可使用 x509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 对于需要唯一初始配置的设备或仅能通过 TPM 或虚拟 TPM 使用 SAS 令牌作为证明机制的设备，建议使用单独注册。 单独注册可能会指定所需 IoT 中心设备 ID。

在门户中注册设备的步骤如下：

1. 请注意设备上用于 HSM 的安全项目。 在开发环境中，可能需要使用前一教程中名为“[提取安全项目](./tutorial-set-up-device.md#extractsecurity)”部分中提到的 API。

1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务。

1. 在“设备预配服务摘要”边栏选项卡上，选择“管理注册”。 根据设备设置选择“单独注册”选项卡或“注册组”选项卡。 单击顶部的“添加”按钮。 选择“TPM”或“X.509”作为标识证明机制，并按前文所述输入适当的安全项目。 可以输入新的“IoT 中心设备 ID”。 完成后，单击“保存”按钮。 

1. 成功注册后，设备应显示在门户中，如下所示：

    ![门户中成功的 TPM 注册](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>启动设备

现在，以下设置已可用于设备注册：

1. 设备或一组设备将向设备预配服务进行注册，并且 
2. 设备已针对 HSM 芯片进行了配置，可通过使用设备预配服务客户端 SDK 的应用程序进行访问。

启动设备可使客户端应用程序开始向设备预配服务进行注册。  


## <a name="verify-the-device-is-registered"></a>验证设备已注册

设备启动后，应进行以下操作。 有关详细信息，请参阅 TPM 模拟器示例应用程序 [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c)。 

1. 设备会向设备预配服务发送注册请求。
2. 对于 TPM 设备，设备预配服务将回复注册质询，设备需对此进行答复。 
3. 注册成功后，设备预配服务会向设备发送 IoT 中心 URI、设备 ID 和加密密钥。 
4. 设备上的 IoT 中心客户端应用程序随后会连接到你的中心。 
5. 成功连接到中心后，设备应出现在 IoT 中心的“Device Explorer”中。 

    ![成功连接到门户中的中心](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

前往下一教程，了解如何跨负载均衡的中心预配多台设备。 

> [!div class="nextstepaction"]
> [跨负载均衡的 IoT 中心预配设备](./tutorial-provision-multiple-hubs.md)

