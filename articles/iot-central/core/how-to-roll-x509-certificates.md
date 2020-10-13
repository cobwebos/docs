---
title: 在 Azure 中滚动 x.509 证书 IoT Central
description: 如何将 x.509 证书与 IoT Central 应用程序一起滚动
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000062"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>如何在 IoT Central 应用程序中滚动 x.509 设备证书

在 IoT 解决方案的生命周期内，需要滚动更新证书。 滚动更新证书的两个主要原因是出现安全违规和证书过期。

如果有安全漏洞，滚动证书是保护系统的最佳安全方案。 作为[假设违规方法](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)的一部分，Microsoft 提倡实施反应式安全流程及预防措施。 这些安全流程应该包括设备证书的滚动更新。 滚动更新证书的频率取决于解决方案的安全需求。 其解决方案涉及到高度敏感数据的客户可以每日滚动更新证书，而其他客户则可以每隔数年滚动更新其证书。


## <a name="obtain-new-x509-certificates"></a>获取新的 x.509 证书

可以使用 OpenSSL 等工具创建自己的 x.509 证书。 此方法非常适合用于测试 X.509 证书，但在安全性方面不能提供很大的保障。 仅使用此方法进行测试，除非您已准备好充当您自己的 CA 提供者。

## <a name="enrollment-groups-and-security-breaches"></a>注册组和安全违规

若要更新组注册以响应安全漏洞，应使用以下方法立即更新当前证书：

1. 在左窗格中导航到 " **管理**  "，然后选择 " **设备连接**"。

2. 选择 " **注册组**"，并在列表中选择组名称。

3. 对于 "证书更新"，选择 " **管理主要** 副本" 或 " **管理辅助**"。

4. 在注册组中添加并验证根 x.509 证书。

   如果主要证书和辅助证书已泄露，请完成这些步骤。

## <a name="enrollment-groups-and-certificate-expiration"></a>注册组和证书过期

如果要滚动证书来处理证书过期，请使用以下方法立即更新当前证书：

1. 在左窗格中导航到 " **管理**  "，然后选择 " **设备连接**"。

2. 选择 " **注册组**"，并在列表中选择组名称。

3. 对于 "证书更新"，选择 " **管理主要**"。

4. 在注册组中添加并验证根 x.509 证书。

5. 稍后在辅助证书过期后，返回并更新该辅助证书。

## <a name="individual-enrollments-and-security-breaches"></a>单独注册和安全违规

如果要滚动证书来响应安全漏洞，请使用以下方法立即更新当前证书：

1. 选择 " **设备**"，并选择设备。

2. 选择 " **连接**"，并选择 "连接方法" 作为 **单个注册**

3. 选择 " **证书 () ** 作为机制"。

    ![管理单独注册](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 对于 "证书更新"，选择文件夹图标，为注册条目选择要上传的新证书。 选择“保存”。

    如果主要证书和辅助证书已泄露，请完成以下步骤

## <a name="individual-enrollments-and-certificate-expiration"></a>单独注册和证书过期

如果滚动更新证书的目的是处理证书过期问题，则应按如下所述使用辅助证书配置，以减少设备尝试预配时造成的停机时间。

当辅助证书接近过期，需要进行汇总时，可以使用主配置进行轮换。 以这种方式在主要证书与辅助证书之间轮换可以减少设备尝试预配时造成的停机时间。

1. 选择 " **设备**"，并选择设备。

2. 选择 " **连接**"，并选择 "连接方法" 作为 **单个注册**

3. 选择 " **证书 () ** 作为机制"。

    ![管理单独注册](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 对于 "辅助证书更新"，选择文件夹图标，为注册条目选择要上传的新证书。 选择“保存”。

5. 稍后在主证书过期后，返回并更新该主要证书。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中滚动 x.509 证书，你可以 [连接到 azure IoT Central](concepts-get-connected.md)。


