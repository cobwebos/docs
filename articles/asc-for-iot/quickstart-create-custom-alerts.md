---
title: 快速入门：为适用于 IoT 的 Azure 安全中心创建自定义警报
description: 了解、创建和分配针对用于 IoT 安全服务的 Azure 安全中心的自定义设备警报。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 063e5c9e7d75fd1c07d148c265b1fe64eee3cbc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303522"
---
# <a name="quickstart-create-custom-alerts"></a>快速入门：创建自定义警报


使用自定义安全组和警报，可以充分利用端到端的安全信息和分类设备知识来确保提高整个 IoT 解决方案的安全性。 

## <a name="why-use-custom-alerts"></a>为何要使用自定义警报？ 

可以最清楚地了解自己的 IoT 设备。

对于完全了解其预期设备行为的客户，适用于 IoT 的 Azure 安全中心可将这种了解转化为设备行为策略，并在背离预期的正常行为时发出警报。

## <a name="security-groups"></a>安全组

使用安全组可以定义设备的逻辑组，并集中管理设备的安全状态。

这些组可以表示具有特定硬件的设备、部署在某个位置的设备，或适合具体需求的其他任何组。

安全组由名为 **SecurityGroup** 的设备孪生标记属性定义。 默认情况下，IoT 中心内的每个 IoT 解决方案都有一个名为 **default** 的安全组。 更改 **SecurityGroup** 属性的值可以更改设备的安全组。
 
例如：

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

使用安全组可将设备分组为逻辑类别。 创建组之后，请将其分配到所选的自定义警报，以最大程度地提高端到端 IoT 安全解决方案的效率。 

## <a name="customize-an-alert"></a>自定义警报

1. 打开 IoT 中心。 
2. 在“安全性”部分中单击“自定义警报”   。 
3. 选择要将自定义设置应用于的安全组。 
4. 单击“添加自定义警报”。 
5. 从下拉列表中选择自定义警报。 
6. 编辑所需的属性，然后单击“确定”。 
7. 请务必单击“保存”。  如果不保存新警报，下一次关闭 IoT 中心时会删除该警报。

 
## <a name="alerts-available-for-customization"></a>可自定义的警报

适用于 IoT 的 Azure 安全中心提供大量的警报，这些警报可根据特定需求进行自定义。 查看[可自定义警报表](concept-customizable-security-alerts.md)可了解警报严重性、数据源、说明以及我们在你收到每个警报时建议的补救步骤。 


## <a name="next-steps"></a>后续步骤

请转到下一篇文章了解如何部署安全代理...

> [!div class="nextstepaction"]
> [部署安全代理](how-to-deploy-agent.md)
