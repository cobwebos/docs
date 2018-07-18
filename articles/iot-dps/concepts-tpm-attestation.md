---
title: Azure IoT 中心设备预配服务 - TPM 证明
description: 本文以概念的方式概述了使用 IoT 设备预配服务的 TPM 证明流。
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 90f41e56f8e95584959576d3e5ad837f4774048a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629076"
---
# <a name="tpm-attestation"></a>TPM 证明

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全的方式预配数百万台设备。

本文介绍使用 [TPM](./concepts-device.md) 时的标识证明过程。 TPM 表示受信任的平台模块，是一类硬件安全模块 (HSM)。 本文假定你使用单独的、固件式的或集成式的 TPM。 软件模拟 TPM 适用于原型制作或测试，但其提供的安全级别不同于单独的、固件式的或集成式的 TPM。 建议不要在生产中使用软件 TPM。 [详细了解](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) TPM 类型。

本文仅适用于特定的设备，这些设备使用提供 HMAC 密钥支持的 TPM 2.0，同时使用认可密钥。 本文不适用于使用 X.509 证书进行身份验证的设备。 TPM 是一种行业通用的 ISO 标准，由 Trusted Computing Group 提出。有关 TPM 的详细信息，可参阅[完整的 TPM 2.0 规范](https://trustedcomputinggroup.org/tpm-library-specification/)或 [ISO/IEC 11889 规范](https://www.iso.org/standard/66510.html)。本文还假定你熟悉公钥和私钥对以及如何将其用于加密。

设备预配服务设备 SDK 处理本文中为你介绍的所有事项。 如果是在自己的设备上使用 SDK，则不需实现任何其他的项目。 本文以概念的方式介绍设备预配时在 TPM 安全芯片上发生的情况，并说明了其安全性高的原因。

## <a name="overview"></a>概述

TPM 使用名为认可密钥 (EK) 的方式作为信任的安全根本。 EK 特定于 TPM，更改它相当于把设备变为新设备。

TPM 还有另一类密钥，称为存储根密钥 (SRK)。 SRK 可以由 TPM 的所有者在取得 TPM 的所有权后生成。 获取 TPM 的所有权就是以 TPM 特有的方式表明“某人在 HSM 上设置密码”。 如果将 TPM 设备出售给新的所有者，新的所有者可以在获取 TPM 的所有权后生成新的 SRK。 重新生成 SRK 可确保以前的所有者无法使用 TPM。 由于 SRK 特定于 TPM 的所有者，因此可以通过 SRK 将数据封装到该所有者的 TPM 本身中。 SRK 为所有者提供存储密钥所需的沙盒，并允许在出售设备或 TPM 的情况下撤销访问权限。 这类似于搬进新屋中：获取所有权就是换门锁，扔掉上一位屋主留下的所有家具 (SRK)，但不能更改新屋的地址 (EK)。

设备在设置好并做好使用准备以后，就会有可供使用的 EK 和 SRK。

![获取 TPM 的所有权](./media/concepts-tpm-attestation/tpm-ownership.png)

关于获取 TPM 所有权的一点说明：获取 TPM 的所有权取决于许多因素，包括 TPM 制造商、所使用的 TPM 工具集以及设备 OS。 请按与系统相关的说明获取所有权。

设备预配服务使用 EK 的公共部分 (EK_pub) 来标识和注册设备。 设备供应商可以在制造或最终测试过程中读取 EK_pub 并将 EK_pub 上传到预配服务，这样就可以在设备通过连接进行预配时识别该设备。 设备预配服务不检查 SRK 或所有者，因此“清除”TPM 会擦除客户数据，但 EK（和其他供应商数据）会保留下来，因此设备在通过连接进行预配时仍会被设备预配服务识别。

## <a name="detailed-attestation-process"></a>详细证明过程

带 TPM 的设备在第一次连接到设备预配服务时，该服务会首先根据存储在注册列表中的 EK_pub 来核对提供的 EK_pub。 如果 EK_pub 不符，则不允许设备预配。 如果 EK_pub 相符，服务就会要求设备通过 nonce 质询证明 EK 专用部分的所有权，该质询是一种用来证明身份的安全质询。 设备预配服务生成一个 nonce，然后使用 SRK 和 EK_pub 先后对其加密。SRK 和 EK_pub 均由设备在初始注册调用过程中提供。 TPM 始终会确保 EK 专用部分的安全。 这样可以防止伪造，确保安全地为获得授权的设备预配 SAS 令牌。

让我们详细探讨证明过程。

### <a name="device-requests-an-iot-hub-assignment"></a>设备请求 IoT 中心分配

首先，设备连接到设备预配服务并请求预配。 为此，设备向服务提供其注册 ID、ID 作用域，以及 TPM 的 EK_pub 和 SRK_pub。 服务将加密的 nonce 传回给设备，要求设备解密该 nonce，然后使用它为 SAS 令牌签名，以便再次进行连接并完成预配。

![设备请求预配](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce 质询

设备获得 nonce 后，会使用 EK 和 SRK 的专用部分将 nonce 解密到 TPM 中；nonce 加密的顺序是将 EK 的信任（不可变）委托给 SRK（在新的所有者获取 TPM 所有权后可以更改）。

![解密 nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>验证 nonce 并接收凭据

然后，设备可以使用解密的 nonce 给 SAS 令牌签名，并使用签名的 SAS 令牌重新建立到设备预配服务的连接。 完成 Nonce 质询以后，服务就会允许设备进行预配。

![设备重新建立到 DPS 的连接，以便验证 EK 所有权](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>后续步骤

设备连接到 IoT 中心以后，你就可以确信设备的密钥已安全地存储。 了解设备预配服务如何使用 TPM 安全地验证设备的标识以后，若要学习更多内容，请查看以下文章：

* [了解自动预配中的所有概念](./concepts-auto-provisioning.md)
* [自动预配入门](./quick-setup-auto-provision.md)：使用 SDK 来管理流。
