---
title: 使用 Azure IoT 中心设备预配服务预配设备 | Microsoft Docs
description: 使用 Azure IoT 中心设备预配服务将设备预配到单个 IoT 中心
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9f151a8fbcdc20124467a1db290f6a05f574e4fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心设备预配服务将设备预配到 IoT 中心

前面的教程介绍了设置设备以连接到设备预配服务的方法。 本教程介绍如何通过此服务使用自动预配和**_注册列表_** 将设备预配到单一 IoT 中心。 本教程演示如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

## <a name="prerequisites"></a>先决条件

继续之前，请确保已按[使用 Azure IoT 中心设备预配服务设置要预配的设备](./tutorial-set-up-device.md)教程所述配置设备。

如果不熟悉自动预配过程，请务必在继续操作之前查看[自动预配概念](concepts-auto-provisioning.md)。

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>注册设备

此步骤需要将设备的唯一安全项目添加到设备预配服务。 这些安全项目基于设备的[证明机制](concepts-device.md#attestation-mechanism)，如下所示：

- 对于基于 TPM 的设备，你需要提供：
    - 特定于每个 TPM 芯片或模拟的“认可密钥”，可以从 TPM 芯片制造商处获得。  请阅读[了解 TPM 认可密钥](https://technet.microsoft.com/library/cc770443.aspx)获取详细信息。
    - 注册 ID，用于在命名空间/作用域内唯一标识设备。 此 ID 可能与设备 ID 相同或不同。 此 ID 是每台设备的必备项。 对于基于 TPM 的设备，可能从 TPM 本身派生注册 ID，例如 TPM 认可密钥的 SHA-256 哈希。

    [![门户中有关 TPM 的注册信息](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- 对于基于 X.509 的设备，你需要提供：
    - [颁发给 X.509（芯片或模拟）的证书](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) 采用 .pem 或 .cer 文件的格式。 对于单独注册，需要对 X.509 系统使用基于设备的“签名人证书”；而对于注册组，则需要使用“根证书”。 

    [![在门户中为 X.509 证明添加单个注册](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

可通过两种方法向设备预配服务注册设备：

- **注册组** 表示共享特定证明机制的一组设备。 对于共享所需初始配置的大量设备，或者全部转到同一租户的设备，建议使用注册组。

    [![在门户中为 X.509 证明添加组注册](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **单独注册** 表示可使用设备预配服务进行注册的单一设备条目。 单独注册可使用 x509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 建议对需要唯一初始配置的设备以及仅能通过 TPM 或虚拟 TPM 使用 SAS 令牌作为证明机制的设备使用单独注册。 单独注册可能会指定所需 IoT 中心设备 ID。

现在，请使用所需的安全项目根据设备的证明机制将设备注册到设备预配服务实例： 

1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务。

2. 在“设备预配服务摘要”边栏选项卡上，选择“管理注册”。 根据设备设置选择“单独注册”选项卡或“注册组”选项卡。 单击顶部的“添加”按钮。 选择“TPM”或“X.509”作为标识证明机制，并按前文所述输入适当的安全项目。 可以输入新的“IoT 中心设备 ID”。 完成后，单击“保存”按钮。 

3. 成功注册后，设备应显示在门户中，如下所示：

    ![门户中成功的 TPM 注册](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

注册后，预配服务会等待设备启动并随后与其连接。 设备首次启动时，客户端 SDK 库将与芯片交互以提取设备中的安全项目，并验证是否已向设备预配服务注册。 

## <a name="start-the-device"></a>启动设备

现在，以下设置已可用于设备注册：

1. 设备或一组设备将向设备预配服务进行注册，并且 
2. 设置在配置证明机制后，已经准备就绪，可以使用设备预配服务客户端 SDK 通过应用程序进行访问了。

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
