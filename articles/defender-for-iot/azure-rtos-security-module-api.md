---
title: Azure RTO 安全模块 API
description: Azure RTO 安全模块的参考 API。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 8191d68f701a3be6a37d8623d2d900f6aa6c1851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934492"
---
# <a name="security-module-for-azure-rtos-api-preview"></a>Azure RTO API (预览的安全模块) 

此 API 仅适用于适用于 Azure RTO 的安全模块。 有关其他资源，请参阅 [AZURE Rto GitHub 资源的安全模块](https://github.com/azure-rtos/azure-iot-preview/releases)。 

## <a name="enable-security-module-for-azure-rtos"></a>启用 Azure RTO 安全模块

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>说明

此例程启用 Azure IoT Security 模块子系统。 内部状态机管理安全事件的集合，并将其发送到 Azure IoT 中心。 只需要一个 NX_AZURE_IOT_SECURITY_MODULE 实例，管理数据收集需要此实例。

### <a name="parameters"></a>参数

| 名称 | 说明 |
|---------|---------|
| nx_azure_iot_ptr [in]    | 指向的指针 `NX_AZURE_IOT` 。  |

### <a name="return-values"></a>返回值

|返回值  |说明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   已成功启用 Azure IoT 安全模块。     |
|NX_AZURE_IOT_FAILURE   |  由于内部错误，未能启用 Azure IoT 安全模块。    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  安全模块需要有效的 #NX_AZURE_IOT 实例。      |

### <a name="allowed-from"></a>允许从

线程数

## <a name="disable-azure-iot-security-module"></a>禁用 Azure IoT 安全模块

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>说明

此例程禁用 Azure IoT Security 模块子系统。

### <a name="parameters"></a>参数

| 名称 | 说明 |
|---------|---------|
| nx_azure_iot_ptr [in]    | 一个指向 `NX_AZURE_IOT` 的指针。 如果为 NULL，则禁用单一实例。 |

### <a name="return-values"></a>返回值

|返回值  |说明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   成功禁用 Azure IoT Security 模块时成功。      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT 中心实例与单一实例复合实例不同。       |
|NX_AZURE_IOT_FAILURE    |  由于内部错误，无法禁用 Azure IoT 安全模块。       |

### <a name="allowed-from"></a>允许从

线程数


## <a name="next-steps"></a>后续步骤

若要了解有关如何开始 Azure RTO 安全模块的详细信息，请参阅以下文章：

- 查看 Defender for IoT RTO 安全模块 [概述](iot-security-azure-rtos.md)。