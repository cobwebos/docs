---
title: Azure 物联网 (IoT) 简介
description: 简单介绍 Azure IoT 和 IoT 服务的基础知识，包括说明如何使用 IoT 的示例。
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2480aa92dec8d8e8879db813b1cfcddc26f77b7e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792520"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Azure 物联网 (IoT) 是什么？

Azure 物联网 (IoT) 是 Microsoft 托管的云服务的集合，这些服务用于连接、监视和控制数十亿项 IoT 资产。 如果以比较简单的术语来表述，则 IoT 解决方案包含一个或多个 IoT 设备，以及一个或多个在云中运行的后端服务，它们可以互相通信。 

本文介绍了 IoT 的基础知识、讨论了各种用例并简单说明了八项不同的可用服务。 了解可用的功能以后，即可确定需要更深入查看的内容，以便设计方案。

## <a name="introduction"></a>介绍

IoT 解决方案的主要部分如下：设备、后端服务以及二者之间的通信。 

### <a name="iot-devices"></a>IoT 设备

设备通常包含带传感器的电路板，这些传感器可以连接到 Internet。 许多设备通过 Wi-Fi 芯片通信。 以下是 IoT 设备的一些示例：

* 远程油泵上的压力传感器
* 空调设备中的温度和湿度传感器
* 电梯中的加速计
* 房间中的存在传感器

频繁用于原型制作的两种设备是 Microsoft 提供的基本 MX 芯片 IoT Devkit，以及 Raspberry PI 设备。 MX 芯片 Devkit 内置了用于温度、压强、湿度的传感器，此外还有陀螺仪、加速计、磁力仪和 Wi-Fi 芯片。 Raspberry PI 是一种 IoT 设备，可以为其附加多种不同的传感器，因此可以根据方案的需要进行具体的选择。 

可以通过 [IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成在设备上运行的应用，以便执行所需任务。 可以通过 SDK 将遥测数据发送到 IoT 中心、从 IoT 中心接收消息和更新，等等。

### <a name="communication"></a>通信

设备可以与后端服务进行双向通信。 下面是设备与后端解决方案通信的方式的几个示例。

#### <a name="examples"></a>示例 

* 设备可能会每隔 5 分钟向 IoT 中心发送一次移动冷藏车提供的温度。 

* 后端服务可能会要求设备更频繁地发送遥测数据，以便诊断问题。 

* 设备可能会根据其从传感器读取的值发送警报。 例如，如果监视化学工厂中的间歇式反应器，则可能需要在温度超出特定值时发送警报。

* 设备可以向仪表板发送信息，供操作人员查看。 例如，冶炼厂中的控制室可以显示每个管道的温度、压强和流量，方便操作员观看。 

此类任务以及其他任务可以通过 [IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 来执行。

#### <a name="connection-considerations"></a>连接注意事项

在 IoT 解决方案中，最大的挑战通常是以安全可靠的方式连接设备。 这是因为，相比于其他客户端（例如浏览器和移动应用），IoT 设备有不同的特征。 具体而言，IoT 设备具有以下特征：

* 通常是无人操作的嵌入式系统（与电话不同）。

* 可以部署到物理访问昂贵的远程位置。

* 可能只能通过解决方案后端来访问。 无法通过其他方式来与设备交互。

* 能力和处理资源可能都有限。

* 网络连接可能不稳定、缓慢或昂贵。

* 可能需要使用专属、自定义或行业特定的应用程序协议。

### <a name="back-end-services"></a>后端服务 

下面是后端服务可以提供的一些功能。

* 接收大规模的来自设备的遥测数据，并确定如何处理和存储该数据。

* 通过分析遥测数据为用户提供见解，不管是实时的还是事后的。

* 从云向特定设备发送命令。 

* 预配设备并控制哪些设备可以连接到基础结构。

* 控制设备状态并监视设备活动。

例如，在预测性维护方案中，云后端存储历史遥测数据。 解决方案根据此数据确定特定泵上可能存在的异常行为，防止其引发真正的问题。 它可以使用数据分析，确定预防性解决方案会将命令发送回设备，以便执行纠正操作。 此过程在设备和云之间产生一个自动反馈循环，大大提高了解决方案效率。

## <a name="an-iot-example"></a>IoT 示例

下面是一个示例，说明了一个公司如何通过 IoT 节省数百万美元。 

有一个很大的畜牧场，养了成千上万的奶牛。 跟踪这么多头奶牛并了解其行为殊非易事，需要开很长时间的车。 他们将传感器连接到每头奶牛身上，将 GPS 坐标和温度之类的信息发送到后端服务并写入到数据库中。

然后，他们通过一项分析服务扫描传入数据，并分析每头奶牛的数据，以便查看下面这样的问题：

* 该奶牛是否发烧？ 该奶牛发烧多长时间了？ 如果发烧时间超过一天，则获取 GPS 坐标并找到该奶牛，然后根据需要为其进行抗生素治疗。 

* 该奶牛呆在同一位置的时间是否超过一天？ 如果是，则获取 GPS 坐标并找到该奶牛。 该奶牛是否从悬崖跌落？ 该奶牛是否受伤？ 该奶牛是否需要帮助？ 

实施此 IoT 解决方案，公司就可以快速查看和治疗奶牛，缩短驾车查看动物所需的时间，节省大量金钱。 如需通过更多现实示例来了解公司如何使用 IoT，请参阅 [Microsoft 的 IoT 技术案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。 

## <a name="iot-services"></a>IoT 服务

Azure 中有多项 IoT 相关服务，确定需要使用哪一项服务可能并不容易。 某些服务（例如 IoT Central 和 IoT 解决方案加速器）提供模板，可以让你快速创建自己的解决方案并入门。 也可使用其他可用服务完整地开发自己的解决方案 -- 这完全取决于你需要多少帮助和多少控制。 下面是一个列表，列出了可用的服务及其功能。

1. [**IoT Central**](../iot-central/overview-iot-central.md)：这是一个 SaaS 解决方案，用于连接、监视和管理 IoT 设备。 若要开始，请针对设备类型选择一个模板，然后创建一个供设备操作员使用的基本的 IoT Central 应用程序并对其进行测试。 此 IoT Central 应用程序也可用于监视设备并预配新设备。 此服务适用于不需深层服务自定义的直观解决方案。 

2. [**IoT 解决方案加速器**](/azure/iot-suite)：这是 PaaS 解决方案的集合，可以用于加快 IoT 解决方案的开发。 可以从提供的 IoT 解决方案着手，然后根据要求对该解决方案进行完整的自定义。 需要 Java 或 .NET 技能才能自定义后端，需要 JavaScript 技能才能自定义可视化效果。 

3. [**IoT 中心**](/azure/iot-hub/)：可以通过此服务从设备连接到 IoT 中心，监视和控制数十亿台 IoT 设备。 这特别适用于需要在 IoT 设备和后端之间进行双向通信的情况。 这是 IoT Central 和 IoT 解决方案加速器的基础服务。 

4. [**IoT 中心设备预配服务**](/azure/iot-dps/)：这是一项适用于 IoT 中心的帮助程序服务，可以用于将设备安全地预配到 IoT 中心。 可以通过此服务轻松且快速地预配数百万台设备，不需逐一预配它们。 

5. [**IoT Edge**](/azure/iot-edge/)：此服务基于 IoT 中心。 它可以用于在 IoT 设备上分析数据，不需在云中进行分析。 通过将部分工作负荷移至边缘，可以减少需要发送到云的消息的数目。 

6. [**Azure 数字孪生**](../digital-twins/index.yml)：此服务用于创建物理环境的综合性模型。 可以为人员、空间和设备之间的关系和交互建模。 例如，可以预测某个工厂的维护需求、分析某个输电网络的实时电力需求，或者优化某个办公室的可用空间的使用。

7. [**时序见解**](/azure/time-series-insights)：此服务用于存储、可视化和查询 IoT 设备生成的大量时序数据。 可以将此服务与 IoT 中心配合使用。 

8. [**Azure Maps**](/azure/azure-maps)：此服务向 Web 和移动应用程序提供地理信息。 有一整套 REST API 和基于 Web 的 JavaScript 控件，可以用于创建灵活的桌面和移动应用程序，同时适用于 Apple 和 Windows 设备。

## <a name="next-steps"></a>后续步骤

如果需要一些使用过的实际商业用例和体系结构，请参阅 [Microsoft Azure IoT 技术案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)。

如果需要一些可以通过 IoT DevKit 来试用的示例项目，请参阅 [IoT DevKit 项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。 

如需不同服务及其使用方式的更全面的说明，请参阅 [Azure IoT 服务和技术](iot-services-and-technologies.md)。

有关 IoT 体系结构的深入介绍，请参阅 [Microsoft Azure IoT 参考体系结构](https://aka.ms/iotrefarchitecture)。
