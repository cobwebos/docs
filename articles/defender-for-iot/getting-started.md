---
title: 部署选项
description: 开始了解用于 IoT 功能和服务的 Defender 的基本工作流。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090057"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Azure Defender for IoT 入门

本文介绍了获取用于 IoT 的 Azure Defender 所需的部署和载入过程。 还需要执行其他步骤。 建议你了解这些步骤，并熟悉随附文档中的信息。

完成所有步骤后，Azure Defender for IoT 传感器将监视你的网络。 根据设置解决方案的方式，还可以将检测发送到本地管理控制台或 IoT 中心。

完成以下步骤可让 Azure Defender 启动并运行 IoT。

## <a name="1-set-up-azure"></a>1. 设置 Azure

- 设置 Azure 帐户。 有关详细信息，请参阅 [创建 Azure 帐户](https://docs.microsoft.com/learn/modules/create-an-azure-account/)。

- 防火墙或代理：如果你有一个配置为允许特定连接的防火墙或类似的干预网络设备，请验证是否为防火墙或代理打开了 *. azure-devices.net:443。 如果不支持通配符，或者需要更多控制，则应在你的 FW 或代理中打开特定的 IoT 中心 FQDN。 有关详细信息，请参阅 [Reference-IoT 中心终结点](/azure/iot-hub/iot-hub-devguide-endpoints)。

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. 将硬件、软件和设备部署到传感器

- 购买传感器硬件并安装软件。 请按照此处概述的步骤进行操作，有关详细信息，请参阅此文章和 [用于 IoT 的 Defender 硬件指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 和 [安装指南](https://aka.ms/AzureDefenderforIoTInstallSensorISO)。

  - 安装传感器后，安全地记录传感器登录凭据。 你将需要这些凭据才能将激活文件上传到传感器。

  - 如果使用本地管理的传感器，请安全地记录传感器的 IP 地址或在安装中定义的传感器名称。 在 IoT 门户的 Defender 中注册传感器时，可能需要使用此名称。 以后可以使用这些方法，以确保在用于 IoT 的 Azure Defender 门户中注册名称和传感器控制台中显示的已部署传感器的 IP 地址之间更轻松地跟踪和一致性。

- 向 IoT 门户注册该传感器，并下载传感器激活文件。

- 将激活文件上传到传感器。

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. 执行传感器监视和管理的网络设置

- 将传感器连接到网络。 [网络设置指南](https://aka.ms/AzureDefenderForIoTNetworkSetup)中所述。

## <a name="4-start-discovering-your-network"></a>4. 开始发现你的网络

- 在传感器控制台中调整系统设置。

- 将传感器连接到本地管理控制台。

有关详细信息，请参阅 [适用于 iot 的 Azure Defender 传感器用户指南](https://aka.ms/AzureDefenderforIoTUserGuide) 和 [用于 iot 本地管理控制台的 defender 用户指南](https://aka.ms/DefenderForIoTManagementConsole)。

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. 用警报信息填充 Azure Sentinel

- 若要将警报信息发送到 Azure Sentinel，请配置 Azure Sentinel： [将数据从用于 IoT 的 Defender 连接到 Azure sentinel](how-to-configure-with-sentinel.md)。
 

## <a name="next-steps"></a>后续步骤

- [为 IoT 启用 Defender](quickstart-onboard-iot-hub.md)
- 配置 [解决方案](quickstart-configure-your-solution.md)
- [创建安全模块](quickstart-create-security-twin.md)
- 配置 [自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)
