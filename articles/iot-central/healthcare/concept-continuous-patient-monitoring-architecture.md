---
title: Azure IoT Central 中的持续患者监视体系结构 | Microsoft Docs
description: 了解持续患者监视解决方案体系结构。
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027478"
---
# <a name="continuous-patient-monitoring-architecture"></a>持续患者监视体系结构

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

利用提供的应用模板，并使用下面概述的体系结构作为指导，可以生成持续患者监视解决方案。

>[!div class="mx-imgBorder"] 
>![CPM 体系结构](media/cpm-architecture.png)

1. 医疗设备通过低耗电蓝牙 (BLE) 进行通信
1. 手机网关接收 BLE 数据并将其发送到 IoT Central
1. 将患者健康状况数据连续导出到 Azure API for FHIR&reg;
1. 基于互操作数据的机器学习
1. 基于 FHIR 数据生成的护理团队仪表板

## <a name="details"></a>详细信息
本节会更详细地概述体系结构图的每个部分。

### <a name="ble-medical-devices"></a>BLE 医疗设备
医疗保健 IoT 领域使用的许多医用可穿戴设备都是低耗电蓝牙设备，这意味着它们无法直接与云通信，而需要通过网关交换信息。 此体系结构建议使用手机应用程序作为此网关。

### <a name="mobile-phone-gateway"></a>手机网关
该手机应用程序的主要功能是从医疗设备引入 BLE 数据，并将其传递给 Azure IoT Central。 此外，该应用可以指导患者完成设备设置和预配流程，还可以帮助他们查看个人健康状况数据概况。 如果是在医院病房内，其他解决方案可以使用平板电脑网关或静态网关实现相同的通信流。

### <a name="export-to-azure-api-for-fhirreg"></a>导出到 Azure API for FHIR&reg;
Azure IoT Central 符合 HIPAA 并且经过 HITRUST&reg; 认证，但还可以将与患者健康状况相关数据发送到 Azure API for FHIR。 [Azure API for FHIR](../../healthcare-apis/overview.md) 是完全托管的、基于标准的、符合法规的临床健康状况数据 API，让你可以用健康状况数据创建新的参与系统。 它由云中托管的平台即服务 (PaaS) 产品/服务提供支持，通过 FHIR API 即可快速交换数据。 使用 IoT Central 的持续数据导出功能，可以将数据发送到 Azure API for FHIR。

### <a name="machine-learning"></a>机器学习
聚合数据并将其转换为 FHIR 格式后，你可以生成机器学习模型，以便提供丰富的见解，让护理团队能够作出明智的决策。 可使用多种服务生成、训练和部署机器学习模型。 有关如何使用 Azure 机器学习产品/服务的详细信息，请参阅[机器学习文档](../../machine-learning/index.yml)。

### <a name="provider-dashboard"></a>提供程序仪表板
可使用位于 Azure API for FHIR 中的数据生成患者见解仪表板，也可以将其直接集成到 EMR 中，帮助护理团队查看患者状态。 护理团队可以使用此仪表板针对需要帮助的患者采取措施，还可以发现病情恶化的早期征兆。 若要了解如何构建 Power BI 实时提供程序仪表板，请遵循[操作指南](howto-health-data-triage.md)。

## <a name="next-steps"></a>后续步骤
* [了解如何部署持续监视患者应用程序模板](tutorial-continuous-patient-monitoring.md)