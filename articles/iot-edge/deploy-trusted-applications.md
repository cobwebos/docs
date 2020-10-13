---
title: 受信任的应用程序 Azure IoT Edge 模块
description: 使用 Open Enclave SDK 和 API 编写受信任的应用程序并将其部署为机密计算 IoT Edge 模块
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361970"
---
# <a name="confidential-computing-at-the-edge"></a>边缘的机密计算

Azure IoT Edge 支持在设备上的 secure enclaves 中运行的受信任的应用程序。 加密在传输或静态时为数据提供安全，但在使用时，enclaves 提供数据和工作负荷的安全性。 IoT Edge 支持打开的 Enclave 作为开发受信任的应用程序的标准。

安全一直是物联网 (IoT) 的重要重点，因为 IoT 设备通常在世界各地，而不是在私有设施内受到保护。 这种曝光会使设备面临篡改和伪造的风险，因为它们在物理上可由不良的执行组件访问。 IoT Edge 设备需要更多的信任和完整性，因为它们允许敏感工作负荷在边缘运行。 与常见传感器和传动器不同，这些智能边缘设备可能会暴露以前仅在受保护的云或本地环境中运行的敏感工作负荷。

[IoT Edge 安全管理器](iot-edge-security-manager.md)解决了一小部分机密计算挑战。 安全管理器使用硬件安全模块 (HSM) 来保护 IoT Edge 设备的标识工作负荷和正在进行的进程。

机密计算的另一个方面是保护正在边缘使用的数据。 *受信任的执行环境* (t) 在处理器上是一种安全的隔离环境，有时称为*enclave*。 *受信任的应用程序*是在 enclave 中运行的应用程序。 由于 enclaves 的性质，受信任的应用程序可以从在主处理器或在中运行的其他应用程序中得到保护。

## <a name="trusted-applications-on-iot-edge"></a>IoT Edge 上的受信任应用程序

受信任的应用程序在传输和静态中加密，并且仅在受信任的执行环境内进行解密。 对于作为 IoT Edge 模块部署的受信任的应用程序，此标准为 true。

开发人员创建受信任的应用程序并将其打包为 IoT Edge 模块。 在将应用程序推送到容器注册表之前对其进行加密。 在 IoT Edge 设备上启动模块之前，应用程序将在整个 IoT Edge 部署过程中保持加密状态。 受信任的应用程序在设备的 "t" 中时，将对其进行解密，并开始执行。

![关系图-在部署到 secure enclave 之前，受信任的应用程序在 IoT Edge 模块内进行加密](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

IoT Edge 上受信任的应用程序是 [Azure 机密计算](../confidential-computing/overview.md)的逻辑扩展。 在云中安全 enclaves 中运行的工作负荷也可以部署到安全 enclaves 中的边缘运行。

## <a name="open-enclave"></a>开放式 Enclave

[Open ENCLAVE SDK](https://openenclave.io/sdk/)是一个开源项目，可帮助开发人员为多个平台和环境创建受信任的应用程序。 Open Enclave SDK 在设备的受信任执行环境中运行，而 Open Enclave API 充当 t 型和非 t 处理环境之间的接口。

Open Enclave 支持多个硬件平台。 IoT Edge 对 enclaves 的支持目前需要开放的可移植的) 操作系统 (操作操作系统。 若要了解详细信息，请参阅 [Open ENCLAVE SDK FOR OP-t o](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)。

打开的 Enclave 存储库还包含帮助开发人员入门的示例。 有关详细信息，请选择其中一个介绍性文章：

* [在 Linux 上构建开放式 Enclave SDK 示例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [在 Windows 上生成开放式 Enclave SDK 示例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>硬件

目前， [TrustBox By Scalys](https://scalys.com/trustbox-industrial/) 是制造商服务协议支持的唯一设备，用于将受信任的应用程序部署为 IoT Edge 模块。 TrustBox 是在 TrustBox 边缘构建的，TrustBox EdgeXL 设备都是通过开放的 Enclave SDK 和 Azure IoT Edge 预先加载的。

有关详细信息，请参阅 [Open Enclave 入门 for The Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started)。

## <a name="develop-and-deploy"></a>开发和部署

准备好开发和部署受信任的应用程序时，适用于 Visual Studio Code 的 [Microsoft Open Enclave](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 扩展可能会有所帮助。 您可以使用 Linux 或 Windows 作为开发计算机，开发 TrustBox 的模块。

## <a name="next-steps"></a>后续步骤

了解如何开始将受信任的应用程序作为带有[Open Enclave 扩展的](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)IoT Edge 模块开发 Visual Studio Code
