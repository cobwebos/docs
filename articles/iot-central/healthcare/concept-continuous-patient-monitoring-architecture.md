---
title: Azure IoT Central 中的持续患者监视体系结构 | Microsoft Docs
description: 教程 - 了解持续患者监视解决方案体系结构。
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ffecd09d1084188195da83568ab3fe32ef2cdaac
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972231"
---
# <a name="continuous-patient-monitoring-architecture"></a>持续患者监视体系结构

本文介绍从“持续患者监视”应用程序模板生成的解决方案的体系结构：

利用提供的应用模板，并使用下面概述的体系结构作为指导，可以生成持续患者监视解决方案。

:::image type="content" source="media/cpm-architecture.png" alt-text="持续患者监视体系结构":::

## <a name="details"></a>详细信息

本节会更详细地概述体系结构图的每个部分：

### <a name="bluetooth-low-energy-ble-medical-devices"></a>蓝牙低功耗 (BLE) 医疗设备

医疗保健 IoT 解决方案中使用的许多医疗可穿戴设备都是 BLE 设备。 这些设备无法直接与云通信，需要使用网关与云解决方案交换数据。 此体系结构使用手机应用程序作为网关。

### <a name="mobile-phone-gateway"></a>手机网关

该手机应用程序的主要功能是从医疗设备收集 BLE 数据，并将其传递给 IoT Central。 该应用还通过设备设置指导患者，让他们查看自己的个人健康数据。 其他解决方案可以使用病房中的平板电脑网关或静态网关。 开源示例移动应用程序可用于 Android 和 iOS，以用作应用程序开发的起点。 若要了解详细信息，请参阅 [IoT Central 持续患者监视移动应用](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)。

### <a name="export-to-azure-api-for-fhirreg"></a>导出到 Azure API for FHIR&reg;

Azure IoT Central 符合 HIPAA 并经过 HITRUST&reg; 认证。 还可以使用 [Azure API for FHIR](../../healthcare-apis/overview.md) 向其他服务发送患者健康状况数据。 Azure API for FHIR 是基于标准的临床健康状况数据 API。 通过[适用于 FHIR 的 Azure IoT 连接器](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart)，可以将 Azure API for FHIR 用作 IoT Central 的连续数据导出目标。

### <a name="machine-learning"></a>机器学习

将机器学习模型与 FHIR 数据一起使用，以生成见解并支持护理团队的决策制定。 若要了解详细信息，请参阅 [Azure 机器学习文档](../../machine-learning/index.yml)。

### <a name="provider-dashboard"></a>提供程序仪表板

使用 Azure API for FHIR 数据构建患者见解仪表板或将其直接集成到护理团队使用的电子病历中。 护理团队可以使用仪表板帮助患者并识别恶化的早期警告迹象。 若要了解详细信息，请参阅[构建 Power BI 提供程序仪表板](howto-health-data-triage.md)教程。

## <a name="next-steps"></a>后续步骤

建议的下一步是[了解如何部署持续监视患者应用程序模板](tutorial-continuous-patient-monitoring.md)。
