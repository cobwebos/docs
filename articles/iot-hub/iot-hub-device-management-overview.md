---
title: 使用 Azure IoT 中心进行设备管理 | Microsoft Docs
description: Azure IoT 中心的设备管理概述：企业设备生命周期和设备管理模式，如重启、恢复出厂设置、固件更新、配置、设备孪生、查询、作业。
author: bzurcher
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: c4d0b5d5ba429dc5d839fa054476461f96a52143
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030727"
---
# <a name="overview-of-device-management-with-iot-hub"></a>使用 IoT 中心进行设备管理的概述

Azure IoT 中心提供功能和可扩展性模型，使设备和后端开发人员可以构建功能强大的设备管理解决方案。 设备的范围从受约束的传感器和单一用途微控制器到功能强大的路由设备组通信的网关。  此外，在不同行业中，IoT 操作员的用例和要求也显著不同。  尽管有此不同，但使用 IoT 中心进行设备管理提供了功能、模式和代码库，以满足不同设备和最终用户的需要。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

创建成功的企业 IoT 解决方案的一个重要部分，是提供操作员如何处理其设备集合的日常管理的策略。 IoT 操作员需要简单且可靠的工具和应用程序，使他们能够重点处理其工作的更具战略意义方面。 本文将提供：

* Azure IoT 中心设备管理方法的简要概述。
* 常见设备管理原则的说明。
* 设备生命周期的说明。
* 常见设备管理模式的概述。

## <a name="device-management-principles"></a>设备管理原则
IoT 带来了一系列独特的设备管理难题，每个企业级解决方案必须满足以下原则：

![设备管理原则图形][img-dm_principles]

* **规模和自动化**：IoT 解决方案需要可以自动执行日常任务的简单工具，使相对数量少的操作人员可以管理数百万台设备。 每天，操作员希望远程批量处理设备操作，并且仅在出现需要直接干预的问题时才收到通知。
* **开放性和兼容性**：该设备生态系统是截然不同的。 管理工具必须进行定制以适应多种设备类、平台和协议。 操作员必须能够支持许多类型的设备，从最受限制的嵌入式单进程芯片到功能强大且全功能的计算机。
* **上下文感知**：IoT 环境是动态的、不断变化的。 服务可靠性极为重要。 设备管理操作必须考虑以下因素，确保进行维护性的停机时，不会影响关键业务运营，也不会产生危险的情况：
    * SLA 设备维护时段
    * 网络和电源状态
    * 使用条件
    * 设备地理位置
* **为许多角色提供服务**：支持 IoT 操作角色的独特工作流和进程至关重要。 操作人员必须与给定约束的内部 IT 部门协调工作。  他们还必须找到可持续方法将实时设备操作信息传递给主管和其他业务管理角色。

## <a name="device-lifecycle"></a>设备生命周期
有一组所有企业 IoT 项目通用的常规设备管理阶段。 在 Azure IoT 中，设备生命周期有五个阶段：

![Azure IoT 设备生命周期的五个阶段：计划、预配、配置、监视、停用][img-device_lifecycle]

在上述五个阶段的每个阶段中，都有几项应满足以提供完整解决方案的设备操作员要求：

* **计划**：使操作员能够创建可让他们轻松且精确地查询的设备元数据方案，并针对一组设备进行批量管理操作。 可以使用设备孪生以标记和属性的形式存储此设备元数据。
  
    其他阅读材料：[设备孪生入门][lnk-twins-getstarted]、[了解设备孪生][lnk-twins-devguide]、[如何使用设备孪生属性][lnk-twin-properties]、[loT 解决方案中设备配置的最佳做法][lnk-adm-best-practices]。
* **预配**：安全地为 IoT 中心预配新设备，使操作员立即发现设备功能。  使用 IoT 中心标识注册表创建灵活的设备标识和凭据，并使用作业成批执行此操作。 通过设备孪生中的设备属性构建设备来报告其功能和情况。
  
    其他阅读材料：[管理设备标识][lnk-identity-registry]、[批量管理设备标识][lnk-bulk-identity]、[如何使用设备孪生属性][lnk-twin-properties]、[loT 解决方案中设备配置的最佳做法][lnk-adm-best-practices]、[Azure IoT 中心设备预配服务][lnk-dps]。
* **配置**：在保持正常运行和安全性的同时便于对设备进行批量配置更改和固件更新。 使用所需属性或通过直接方法和广播作业成批执行这些设备管理操作。
  
    其他阅读材料：[如何使用设备孪生属性][lnk-twin-properties]、[大规模配置和监视 loT 设备][lnk-adm-how-to]、[loT 解决方案中设备配置的最佳做法][lnk-adm-best-practices]。
* **监视**：监视总体设备集合运行状况、正在进行的操作的状态并针对可能需要操作员注意的问题向操作员发出警报。  应用设备孪生以允许设备报告实时操作情况和更新操作的状态。 使用设备孪生查询生成显示最直接问题的功能强大的仪表板报告。
  
    其他阅读材料：[如何使用设备孪生属性][lnk-twin-properties]、[适用于设备孪生、作业和消息路由的 IoT 中心查询语言][lnk-query-language]、[大规模配置和监视 IoT 设备][lnk-adm-how-to]、[loT 解决方案中设备配置的最佳做法][lnk-adm-best-practices]。
* **停用**：在发生故障、升级周期后，或在服务生存期结束时更换或停用设备。  使用设备孪生来维护设备信息（如果正在更换物理设备），或者将设备信息存档（如果正在停用设备）。 使用 IoT 中心标识注册表来安全地撤销设备标识和凭据。
  
    *其他阅读材料*：[如何使用设备孪生属性][lnk-twin-properties]、[管理设备标识][lnk-identity-registry]。

## <a name="device-management-patterns"></a>设备管理模式
IoT 中心启用以下设备管理模式集。  [设备管理教程][lnk-get-started]更详细地介绍如何扩展这些模式以适合具体方案，以及如何基于这些核心模板设计新模式。

* **重启** - 后端应用通过直接方法通知设备它已启动重启。  设备使用报告属性来更新设备的重新启动状态。
  
    ![设备管理重新启动模式图形][img-reboot_pattern]
* **恢复出厂设置** - 后端应用通过直接方法通知设备它已启动恢复出厂设置。  设备使用报告属性来更新设备的恢复出厂设置状态。
  
    ![设备管理恢复出厂设置模式图形][img-facreset_pattern]
* **配置** - 后端应用使用所需属性来配置设备上运行的软件。  设备使用报告属性来更新设备的配置状态。
  
    ![设备管理配置模式图形][img-config_pattern]
* **固件更新** - 后端应用程序使用自动设备管理配置，以选择接收更新的设备、指示设备在哪里查找更新，以及监视更新过程。 设备启动多步骤过程，用于下载、验证和应用固件映像，然后在重新连接到 IoT 中心服务前重启设备。  在整个多步骤过程中，设备使用报告属性来更新设备的进度和状态。
  
    ![设备管理固件更新模式图形][img-fwupdate_pattern]
* **报告进度和状态** - 解决方案后端在一组设备上运行设备孪生查询，以报告设备上运行的操作的状态和进度。
  
    ![设备管理报告进度和状态模式图形][img-report_progress_pattern]

## <a name="next-steps"></a>后续步骤
可以使用 IoT 中心设备管理提供的功能、模式和代码库，在每个设备生命周期阶段创建满足企业 IoT 操作员需求的 IoT 应用程序。

若要继续了解 IoT 中心设备管理功能，请参阅[设备管理入门][lnk-get-started]教程。

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-node-node-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-adm-best-practices]: iot-hub-configuration-best-practices.md
[lnk-adm-how-to]: iot-hub-auto-device-config.md