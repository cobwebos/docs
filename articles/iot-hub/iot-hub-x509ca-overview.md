---
title: Azure IoT 中心 X.509 CA 安全性概述 | Microsoft Docs
description: 概述 - 如何使用 X.509 证书颁发机构在 IoT 中心对设备进行身份验证。
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 11f340ebe1719c680cce5f6ad34150d3717c57d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="device-authentication-using-x509-ca-certificates"></a>使用 X.509 CA 证书进行设备身份验证

本文介绍如何使用 X.509 证书颁发机构 (CA) 证书对连接 IoT 中心的设备进行身份验证。  在本文中，将了解：

* 如何获取 X.509 CA 证书
* 如何将 X.509 CA 证书注册到 IoT 中心
* 如何使用 X.509 CA 证书为设备签名
* 如何对使用 X.509 CA 签名的设备进行身份验证

## <a name="overview"></a>概述

借助 X.509 CA 功能，可以使用证书颁发机构 (CA) 在 IoT 中心进行设备身份验证。 此功能极大地简化了初始设备登记过程，以及设备制造期间的供应链后勤。 [在这篇有关使用 X.509 CA 证书进行设备身份验证所获得的价值的方案文章中了解详细信息](iot-hub-x509ca-concept.md)。  我们建议在继续学习之前先阅读此方案文章，因为其中解释了为何要执行本文稍后所述的步骤。

## <a name="prerequisite"></a>先决条件

使用 X.509 CA 功能需要有一个 IoT 中心帐户。  [了解如何创建 IoT 中心实例](iot-hub-csharp-csharp-getstarted.md)（如果没有）。

## <a name="how-to-get-an-x509-ca-certificate"></a>如何获取 X.509 CA 证书

X.509 CA 证书位于每个设备的证书链的顶层。  可以根据目标用途购买或创建该证书。

对于生产环境中，我们建议从公共根证书颁发机构购买 X.509 CA 证书。 购买 CA 证书的好处是可让根 CA 充当受信任的第三方，确保设备的合法性。 如果希望设备加入开放的 IoT 网络，并且预期设备在该网络中与第三方产品或服务交互，请考虑此选项。

也可以创建自签名的 X.509 CA 用于试验，或者在闭合型 IoT 网络中使用。

不管 X.509 CA 证书是如何获取的，都请确保保持其相应私钥的机密性，并随时对此私钥进行保护。  这是确保能够在 X.509 CA 身份验证中建立信任的必要措施。 

了解如何[创建自签名的 CA 证书](iot-hub-security-x509-create-certificates.md#createcerts)（在这整篇功能介绍中可用于试验）。

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>在证书信任链中为设备签名

X.509 CA 证书的所有者能以加密方式为某个中间 CA 签名，而该 CA 又能为另一个中间 CA 签名，依此类推，直到最后一个中间 CA 通过为设备签名来终止此过程。 结果是一个称为证书信任链的级联证书链。 在现实生活中，此过程的运作方式是向签名设备委托信任。 这种委托十分重要，因为它能够建立一种加密的可变监护链，并避免共享签名密钥。

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

在此处了解如何像为设备签名时一样[创建证书链](iot-hub-security-x509-create-certificates.md#createcertchain)。

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>如何将 X.509 CA 证书注册到 IoT 中心

将 X.509 CA 证书注册到 IoT 中心，在注册和连接期间，此证书将用于对设备进行身份验证。  注册 X.509 CA 证书的过程由两个步骤构成，包括证书文件上传和所有权证明。

上传过程需要上传包含证书的文件。  此文件不得包含任何私钥。

所有权证明步骤涉及到在你与 IoT 中心之间执行加密质询和响应过程。  假设数字证书内容是公开的，因而容易遭到窃听，则 IoT 中心需要确认 CA 证书真正由你拥有。  为此，IoT 中心将会生成一个必须使用 CA 证书的相应私钥签名的随机质询。  如果如前所述保持私钥的机密性并对其进行保护，则只有你才能拥有所需的信息来完成此步骤。 私钥的机密性是此方法的信任源。  为质询签名后，可通过上传包含结果的文件来完成此步骤。

在此处了解[如何注册 CA 证书](iot-hub-security-x509-get-started.md#registercerts)。

## <a name="how-to-create-a-device-on-iot-hub"></a>如何在 IoT 中心创建设备

为了防止设备模拟，IoT 中心要求告知设备的预期。  为此，可在 IoT 中心的设备注册表中创建一个设备条目。  使用 IoT 中心[设备预配服务](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (DPS) 时，此过程会自动化。 

在此处了解如何[在 IoT 中心手动创建设备](iot-hub-security-x509-get-started.md#createdevice)。

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>对使用 X.509 CA 证书签名的设备进行身份验证

注册 X.509 CA 证书并在证书信任链中为设备签名后，剩余的操作就是在设备连接时（甚至包括首次连接）进行设备身份验证。  当 X.509 CA 签名的设备建立连接时，会上传其证书链用于验证。 该链包含所有中间 CA 和设备证书。  使用此信息，IoT 中心可通过一个两步过程对设备进行身份验证。  IoT 中心以加密方式验证证书链的内部一致性，然后向设备发出所有权证明质询。  如果设备返回了所有权证明成功响应，则 IoT 中心会声明该设备可信。  此声明假设设备的私钥受到保护，并且只有该设备才能成功响应此质询。  我们建议使用设备中的安全芯片（例如硬件安全模块 (HSM)）来保护私钥。

设备成功连接到 IoT 中心后，身份验证过程即告完成，这也表明设置正确。

在此处了解如何[完成此设备连接步骤](iot-hub-security-x509-get-started.md#authenticatedevice)。

## <a name="next-steps"></a>后续步骤

了解 IoT 中 [X.509 CA 身份验证的价值](iot-hub-x509ca-concept.md)。

开始使用 IoT 中心[设备预配服务](https://docs.microsoft.com/azure/iot-dps/)。
