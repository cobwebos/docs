---
title: 了解 IoT 基线的 Azure 安全中心*微软文档
description: 了解 IoT 基线的 Azure 安全中心的概念。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176613"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>用于 IoT 基线和自定义检查的 Azure 安全中心

本文介绍了 IoT 基线的 Azure 安全中心，并总结了基线自定义检查的所有相关属性。

## <a name="baseline"></a>基线

基线为每个设备建立标准行为，并更容易确定异常行为或偏离预期规范。  

## <a name="baseline-custom-checks"></a>基线自定义检查

基线自定义检查使用设备的**模块标识孪生**为每个设备基线建立自定义检查列表。 

## <a name="setting-baseline-properties"></a>设置基线属性

1. 在 IoT 中心中，找到并选择要更改的设备。
1. 单击设备，然后单击**Azureiot 安全**模块。
1. 单击**模块标识孪生**。
1. 将**基准自定义检查**文件上载到设备。
1. 将基线属性添加到安全模块，然后单击"**保存**"。

### <a name="baseline-custom-check-file-example"></a>基线自定义检查文件示例

要配置基线自定义检查：

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>基线自定义检查属性

| “属性”| 状态 | 有效值| 默认值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|基线自定义检查启用|必需： 真 |有效值：**布尔** |默认值：**假** |发送高优先级消息之前的最大时间间隔。|
|基线自定义检查文件路径 |必需： 真|有效值：**字符串**， **null** |默认值 **：null** |基线 xml 配置的完整路径|
|基线自定义检查文件哈希 |必需： 真|有效值：**字符串**， **null** |默认值 **：null** |`sha256sum`xml 配置文件的。 有关详细信息，请使用[sha256sum 参考](https://linux.die.net/man/1/sha256sum)。 |

要查看其他基线示例，请参阅[自定义基线示例 -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml)和[自定义基线示例 -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)。

## <a name="next-steps"></a>后续步骤

- 访问[原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并探讨[安全建议](concept-recommendations.md)
- 了解和探索[安全警报](concept-security-alerts.md)
