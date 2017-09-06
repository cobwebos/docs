---
title: "自定义连接了 Azure IoT 套件的工厂 | Microsoft Docs"
description: "介绍如何自定义已连接工厂预配置解决方案的行为。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 90a6172dbd887ecda5a9f5d9082a4e136092bc10
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>自定义已连接工厂解决方案如何显示来自 OPC UA 服务器的数据

## <a name="introduction"></a>介绍

已连接工厂解决方案将聚合并显示来自连接到该解决方案的 OPC UA 服务器的数据。 可以在解决方案中浏览命令并将其发送到 OPC UA 服务器。 有关 OPC UA 的详细信息，请参阅[连接工厂常见问题解答](iot-suite-faq-cf.md)。

解决方案中聚合数据的示例包括设备综合效率 (OEE) 和关键性能指标 (KPI)，可在工厂的仪表板、生产线和工作站级别查看这些数据。 以下屏幕截图显示慕尼黑工厂中生产线 1 上装配工作站的 OEE 和 KPI 值：

![解决方案中 OEE 和 KPI 值的示例][img-oee-kpi]

在此解决方案中，可以查看来自名为“stations”的 OPC UA 服务器上特定数据项目的详细信息。 以下屏幕截图显示特定工作站中已生产产品数的条形图：

![已生产产品数的条形图][img-manufactured-items]

单击其中一个图形，即可使用时序见解 (TSI) 进一步探索数据：

![使用时序见解探索数据][img-tsi]

本文介绍：

- 如何使数据适用于解决方案中的各种视图。
- 如何自定义解决方案显示数据的方式。

## <a name="data-sources"></a>数据源

已连接工厂解决方案显示来自连接到解决方案的 OPC UA 服务器的数据。 默认安装包括运行工厂模拟的多台 OPC UA 服务器。 可将自己的 OPC UA 服务器（[通过网关连接][lnk-connect-cf]）添加到解决方案。

可以在仪表板中浏览已连接的 OPC UA 数据库可向解决方案发送的数据项目：

1. 导航到“选择 OPC UA 服务器”视图：

    ![导航到“选择 OPC UA 服务器”视图][img-select-server]

1. 选择一台服务器，并单击“连接”。 出现安全警告时，单击“继续”。

    > [!NOTE]
    > 此警告针对每个服务器仅出现一次，它在解决方案仪表板和服务器之间建立信任关系。

1. 现可浏览服务器可向解决方案发送的数据项目。 待发送到解决方案的数据项目带有绿色复选标记：

    ![已发布的项目][img-published]

1. 如果是解决方案中的管理员，可以发布数据项目，使其在已连接的工厂解决方案中可用。 管理员还可在 OPC UA 服务器中更改数据项目的值以及调用方法。

## <a name="map-the-data"></a>映射数据

已连接的工厂解决方案将 OPC UA 服务器中的已发布数据项目映射并集成到解决方案中的各种视图。 预配解决方案时，已连接的工厂解决方案会部署到 Azure 帐户。 已连接 Visual Studio 的工厂解决方案中的 JSON 文件存储此映射信息。 可以在已连接的工厂 Visual Studio 解决方案中查看和修改此 JSON 配置文件。 在进行更改后，可重新部署解决方案。

配置文件可用于：

- 编辑现有的模拟工厂、生产线和工作站。
- 映射来自连接到解决方案的实际 OPC UA 服务器的数据。

若要克隆已连接的工厂 Visual Studio 解决方案的副本，请使用以下 git 命令：

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

ContosoTopologyDescription.json 文件定义从 OPC UA 服务器数据项目到已连接的工厂解决方案仪表板中视图的映射。 可以在 Visual Studio 解决方案中 WebApp 项目的 Contoso\Topology 文件夹中找到此配置文件。

JSON 文件的内容按工厂、生产线和工作站节点的层次结构进行组织。 此层次结构定义已连接的工厂仪表板中的导航层次结构。 该层次结构每个节点处的值决定仪表板中显示的信息。 例如，JSON 文件包含慕尼黑工厂的以下值：

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

名称、说明和位置在仪表板的此视图中显示：

![仪表板中的慕尼黑工厂数据][img-munich]

每个工厂、生产线和工作站均有一个图像属性。 可以在 WebApp 项目的 Content\img 文件夹中找到这些 JPEG 文件。 已连接的工厂仪表板中显示了这些图像文件。

每个工作站包括多个详细属性，用于定义从 OPC UA 数据项目的映射。 下面各节介绍这些属性：

### <a name="opcuri"></a>OpcUri

OpcUri 值是唯一标识 OPC UA 服务器的 OPC UA 应用程序 URI。 例如，慕尼黑工厂中生产线 1 上装配工作站的 OpcUri 值如下所示：urn:scada2194:ua:munich:productionline0:assemblystation。

可在解决方案仪表板中查看已连接 OPC UA 服务器的 URI：

![查看 OPC UA 服务器 URI][img-server-uris]

### <a name="simulation"></a>模拟

Simulation 节点中的信息特定于在默认预配的 OPC UA 服务器中运行的 OPC UA 模拟。 不用于实际的 OPC UA 服务器。

### <a name="kpi1-and-kpi2"></a>Kpi1 和 Kpi2

这些节点说明工作站中的数据如何作用于仪表板中的两个 KPI 值。 在默认部署中，这两个 KPI 值分别代表每小时生产的部件数和每小时的耗电量（千瓦时）。 此解决方案在工作站级别计算 KPI 值，并在生产线和工厂级别将其聚合。

每个 KPI 具有最小值、最大值和目标值。 每个 KPI 值还可以定义要执行的已连接的工厂解决方案的警报操作。 下面的代码段显示慕尼黑工厂中生产线 1 上装配工作站的 KPI 定义：

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

下面的屏幕截图在仪表板中显示 KPI 数据。

![仪表板中的 KPI 信息][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

OpcNodes 节点标识 OPC UA 服务器中的已发布数据项目，并指定如何处理该数据。

NodeId 值标识 OPC UA 服务器中的特定 OPC UA NodeID。 慕尼黑工厂中生产线 1 上装配工作站的第一个节点具有值 ns=2;i=385。 NodeId 值指定要从 OPC UA 服务器中读取的数据项目，SymbolicName 提供要在仪表板中使用的该数据的用户友好名称。

下表汇总了与每个节点相关联的其他值：

| 值 | 说明 |
| ----- | ----------- |
| 相关性  | 此数据作用到的 KPI 和 OEE 值。 |
| OpCode     | 数据的聚合方式。 |
| 单位      | 要在仪表板中使用的单位。  |
| Visible    | 是否在仪表板中显示此值。 某些值在计算中使用，但不会显示。  |
| 最大值    | 在仪表板中触发警报的最大值。 |
| MaximumAlertActions | 响应警报所采取的操作。 例如，将命令发送到工作站。 |
| ConstValue | 计算中使用的常量值。 |

## <a name="deploy-the-changes"></a>部署更改

完成对 ContosoTopologyDescription.json 文件的更改后，必须将已连接的工厂解决方案重新部署到 Azure 帐户。

azure-iot-connected-factory 存储库包含 build.ps1 PowerShell 脚本，该脚本可用于重新构建并部署解决方案。

## <a name="next-steps"></a>后续步骤

若要详细了解已连接的工厂预配置解决方案，请阅读下列文章：

* [连接工厂预配置解决方案演练][lnk-rm-walkthrough]
* [为已连接的工厂部署网关][lnk-connect-cf]
* [azureiotsuite.com 站点权限][lnk-permissions]
* [连接的工厂常见问题解答](iot-suite-faq-cf.md)
* [常见问题解答][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
