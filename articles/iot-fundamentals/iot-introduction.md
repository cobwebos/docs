---
title: Azure 物联网 (IoT) 简介
description: Azure IoT 和相关服务及技术概述。
author: BryanLa
manager: timlt
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 05/18/2018
ms.author: bryanla
ms.openlocfilehash: 858124ca92ae80313749abbd22c479fef90ce873
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081517"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Azure 和物联网简介

Azure IoT 包含三大领域的技术和解决方案 - 解决方案、平台服务和 Edge，所有这些都为了提升 IoT 应用程序的端到端开发。 本文从介绍云中的 IoT 解决方案的常见特征开始，然后概述 Azure IoT 如何解决 IoT 项目的挑战以及建议你采用 Azure IoT 的原因。

## <a name="iot-solution-architecture"></a>IoT 解决方案体系结构

IoT 解决方案需要设备（可能数以百万计）与解决方案后端之间有安全的双向通信。 例如，解决方案可能会使用自动化的预测分析，从设备到云的事件流中挖掘有用的见解。 

下图显示了典型 IoT 解决方案体系结构的关键元素。 此图忽略了具体的实现细节，例如所用 Azure 服务以及设备操作系统。 在此体系结构中，IoT 设备收集其发送到云网关的数据。 云网关使其他后端服务能够处理数据。 这些后端服务可以将数据传送到：

* 其他业务线应用程序。
* 通过仪表板或其他呈现设备进行操作的操作人员。

![IoT 解决方案体系结构][img-solution-architecture]

> [!NOTE]
> 有关 IoT 体系结构的深入介绍，请参阅 [Microsoft Azure IoT 参考体系结构][lnk-refarch]。

### <a name="device-connectivity"></a>设备连接

在 IoT 解决方案体系结构中，设备通常将遥测数据发送到云进行存储和处理。 例如，在预测性维护方案中，解决方案后端可以使用传感器数据流来判断特定的泵何时需要维护。 设备还可以通过读取来自云终结点的消息，接收和响应云到设备的消息。 在同一示例中，解决方案后端可将消息发送到泵站中的其他泵，以便在维护应开始之前先重新路由流量。 此过程可确保维护工程师到场后即可开始工作。

在 IoT 解决方案中，最大的挑战通常是以安全可靠的方式连接设备。 这是因为，相比于其他客户端（例如浏览器和移动应用），IoT 设备有不同的特征。 具体而言，IoT 设备具有以下特征：

* 通常是无人操作的嵌入式系统（与电话不同）。
* 可以部署到物理访问昂贵的远程位置。
* 可能只能通过解决方案后端来访问。 无法通过其他方式来与设备交互。
* 能力和处理资源可能都有限。
* 网络连接可能不稳定、缓慢或昂贵。
* 可能需要使用专属、自定义或行业特定的应用程序协议。
* 可以使用大量常见的硬件和软件平台来创建。

除了前述约束，任何 IoT 解决方案还必须是可缩放的、安全的和可靠的。

设备可以与云直接通信，也可以通过中介性网关与云通信，具体取决于通信协议和网络可用性。 IoT 体系结构通常混合使用这两种通信模式。

### <a name="data-processing-and-analytics"></a>数据处理和分析

在现代 IoT 解决方案中，数据处理可在云中或设备端进行。 设备端处理称为“边缘计算”。 数据处理位置的选择取决于多项因素，例如：

* 网络约束。 如果设备和云之间的带宽有限，则需进行更多的边缘处理。
* 响应时间。 如果需要在设备上进行近实时的操作，则最好是在设备中处理响应。 例如，紧急情况下需停止机器臂。
* 法定环境。 某些数据不能发送到云。

通常情况下，在边缘和云中进行的数据处理都是以下功能的组合：

* 接收大规模来自设备的遥测数据，并确定如何处理和存储该数据。
* 通过分析遥测数据为用户提供见解，不管是实时的还是事后的。
* 从云或网关设备向特定设备发送命令。

另外，IoT 云后端应提供：

* 设备注册功能，用于：
    * 预配设备。
    * 控制哪些设备有权连接到基础结构。
* 设备管理，目的是控制设备状态并监视设备活动。

例如，在预测性维护方案中，云后端存储历史遥测数据。 解决方案根据此数据确定特定泵上可能存在的异常行为，防止其引发真正的问题。 它可以使用数据分析，确定预防性解决方案会将命令发送回设备，以便执行纠正操作。 此过程在设备和云之间产生一个自动反馈循环，大大提高了解决方案效率。

### <a name="presentation-and-business-connectivity"></a>呈现和业务连接

呈现和业务连接层可让最终用户与 IoT 解决方案及设备交互。 它可让用户查看和分析从其设备收集的数据。 这些视图可以采用仪表板或 BI 报表的格式，以显示历史数据和/或接近实时的数据。 例如，操作员可检查特定抽水站的状态，并查看系统引发的任何警报。 还可以通过此层来集成 IoT 解决方案与现有业务线应用程序，将其绑定到企业业务流程或工作流中。 例如，预测性维护解决方案可集成计划系统，在解决方案识别出需要维护的泵时预约工程师到泵站进行检查。

## <a name="why-azure-iot"></a>为什么选择 Azure IoT？

Azure IoT 简化了 IoT 项目的复杂性，并解决了诸多问题，如：安全性、体系结构不兼容性和缩放 IoT 解决方案。 下面为解决方法：

**敏捷** <br>
加速 IoT 进程
* 规模：从小规模开始，随时随地扩展到任何规模 - 数百万台设备和 TB 级的数据，遍布全世界的大部分区域。

* 开放：通过连接到任何设备、软件或服务来使用你所拥有的资源或实现现代化的未来。

* 混合：通过在 Edge、云中或两者之间的任意位置部署 IoT 解决方案实现按需生成。

* 节奏：更快地部署，加快上市速度，并在解决方案加速器和 IoT 创新竞争中保持领先。

**综合** <br>
对业务产生影响力

* 完整：Microsoft 是唯一一家具有覆盖设备到云的完整平台的 IoT 解决方案提供商，涵盖大数据、高级分析并具有托管服务。

* 实现成功的合作伙伴：充分利用全球最大合作伙伴生态系统的力量，在行业和世界范围内，将业务线和技术带到生活中。

* 数据驱动：IoT 以数据为本，最佳的 IoT 解决方案聚集了存储、解释、转换、分析，以及在适当的时间地点将数据呈现给正确的用户所需的所有工具。

* 设备中心化：Microsoft IoT 可连接任何内容，从旧设备到庞大的已认证硬件生态系统，并具有跨 Edge、移动设备和内嵌系统构建设备的能力。

**安全** <br>
解决 IoT 最困难的部分 -安全

* 强大能力：借助 Microsoft IoT，可将设想、技术和最佳做法相结合，并且能够解决 IoT 最困难的部分 - 安全性。

* 付诸行动：利用标识和访问管理、威胁和信息保护以及安全管理，保护 IoT 数据安全并管理风险。

* 高枕无忧：保证设备、软件、应用程序和云服务以及本地环境中的敏感信息的安全。

* 合规性：Microsoft 始终在建立安全要求方面处于行业领先，符合 IoT 设备、数据和服务广泛的国际和行业特定标准。

## <a name="next-steps"></a>后续步骤

探讨以下领域的技术和解决方案，或查看左侧目录了解 Azure IoT 服务列表。

<ul class="panelContent cardsF">  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>解决方案</h3>
                        <a href="/azure/iot-suite">IoT 解决方案加速器</a><br/>
  
                        <a href="/azure/iot-central">IoT Central</a>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>平台服务</h3>
                        <a href="/azure/iot-hub">IoT 中心</a><br/>
                        <a href="/azure/iot-dps">IoT 中心设备预配服务</a><br/>
                        <a href="/azure/azure-maps">Maps</a><br/>
                        <a href="/azure/time-series-insights">时序见解</a>
                    </div>
                </div>
            </div>
        </div>
    </li>  
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>Microsoft Edge</h3>
                        <a href="/azure/iot-edge">IoT Edge</a><br/>
                        <a href="/azure/iot-edge/how-iot-edge-works">什么是 IoT Edge？</a>
                    </div>
                </div>
            </div>
        </div>
    </li>      
</ul>

[img-paas-saas-technologies-solutions]: media/index/paas-saas-technologies-solutions.png
[img-solution-architecture]: ./media/iot-introduction/iot-reference-architecture.png
[img-dashboard]: ./media/iot-introduction/iot-suite.png

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-iot-central-land]: https://docs.microsoft.com/microsoft-iot-central/
[lnk-iot-dps-land]: /azure/iot-dps/index.yml
[lnk-iot-edge-land]: /azure/iot-edge/index.yml
[lnk-iot-hub-land]: /azure/iot-hub/index.md
[lnk-iot-maps-land]: /azure/maps/index.yml
[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[lnk-iot-tsi-land]: /azure/time-series-insights/index.yml

[lnk-iot-hub]: ../iot-hub/about-iot-hub.md
[lnk-iot-sa]: ../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[lnk-protocol-gateway]:  ../iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: https://aka.ms/iotrefarchitecture


