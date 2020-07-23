---
title: 有关 Azure 中的 FHIR 服务的常见问题解答 - Azure API for FHIR
description: 获取有关 Azure API for FHIR 的常见问题（例如 FHIR API 后面的数据的存储位置以及版本支持）的解答。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536719"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>有关 Azure API for FHIR 的常见问题解答

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

### <a name="what-is-fhir"></a>什么是 FHIR？
快速医疗保健互操作性资源（FHIR）是一种互操作性标准，旨在允许在不同的运行状况系统之间交换医疗保健数据。 此标准由 HL7 组织开发，由世界各地的医疗保健组织使用。 可用 FHIR 的最新版本为 R4 （版本4）。 适用于 FHIR 的 Azure API 支持 R4，并且还支持早期版本 STU3 （Standard for 试用版）。 有关 FHIR 的详细信息，请访问[HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 是否在 Azure 中存储的数据？

是的，数据存储在 Azure 中的托管数据库中。 适用于 FHIR 的 Azure API 不提供对基础数据存储区的直接访问。

### <a name="what-identity-provider-do-you-support"></a>你支持哪些标识提供程序？

目前支持 Microsoft Azure Active Directory 作为标识提供者。

### <a name="what-fhir-version-do-you-support"></a>你支持哪个 FHIR 版本？

我们支持 Azure API for FHIR （PaaS）和适用于 Azure 的 FHIR 服务器（开源）上的4.0.0 和3.0.1 版本。

有关详细信息，请参阅[支持的功能](fhir-features-supported.md)。 了解[HL7 FHIR 的版本历史记录](https://hl7.org/fhir/R4/history.html)中的不同版本之间的更改。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>用于 Azure 的开源 Microsoft FHIR 服务器与用于 FHIR 的 Azure API 之间有何区别？

适用于 FHIR 的 Azure API 是 Azure 的开源 Microsoft FHIR 服务器托管和托管版本。 在托管服务中，Microsoft 提供所有维护和更新。 

当你运行适用于 Azure 的 FHIR 服务器时，你可以直接访问基础服务。 不过，如果要存储 PHI 数据，还需要负责维护和更新服务器以及所有必需的符合性工作。

从开发的角度来看，每项功能都首先部署到开源 Microsoft FHIR Server for Azure。 在开源中验证后，它将发布到 PaaS Azure API for FHIR 解决方案。 在开源和 PaaS 版本之间的时间取决于功能的复杂性以及其他路线图优先级。 

### <a name="what-is-smart-on-fhir"></a>什么是 FHIR 的智能？

FHIR 上的智能（可替换医疗应用程序和可重用技术）是一组开放式规范，可将合作伙伴应用程序与 FHIR 服务器和其他运行状况 IT 系统（如电子健康记录和健康信息交换）集成。 通过创建智能的 FHIR 应用程序，你可以确保你的应用程序可以通过不同系统的很多进行访问和利用。
用于 FHIR 的身份验证和 Azure API。 若要了解有关智能的详细信息，请访问[智能运行状况](https://smarthealthit.org/)。


## <a name="iot-connector-preview"></a>IoT 连接器（预览）

### <a name="what-is-iomt"></a>什么是 IoMT？
IoMT 代表医疗网，它是一类 IoT 设备，可通过网络与其他医疗保健 IT 系统一起捕获和交换健康和 wellness 数据。 IoMT 设备的一些示例包括健身和临床可穿戴设备、监视传感器、活动跟踪器、护理网亭，甚至智能欣然。

### <a name="how-many-iot-connectors-do-i-need"></a>我需要多少个 IoT 连接器？
单个 IoT 连接器可用于从大量不同类型的设备中引入数据。 由于以下原因，你可能仍会决定使用不同的连接器：
- **规模**：对于公共预览版，IoT 连接器资源容量是固定的，应每秒提供大约200消息的吞吐量。 如果需要更高的吞吐量，则可以添加更多的 IoT 连接器。
- **设备类型**：对于设备管理原因，你可以为每种类型的 IoMT 设备设置单独的 IoT 连接器。

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>公共预览期间 IoT 连接器的数量是否有限制？
是的，当功能处于公共预览阶段时，只能为每个订阅创建两个 IoT 连接器。 存在此限制是为了防止意外支出，因为在预览期间，该功能可免费使用。 请求达到此限制时，最多可以引发5个 IoT 连接器。

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>公共预览版期间，哪些 Azure 区域 IoT 连接器功能可用？
IoT 连接器适用于适用于 FHIR 的 Azure API 的所有 Azure 区域。

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>能否为 IoT 连接器配置扩展容量？
由于 IoT 连接器在公共预览版期间免费，因此其缩放容量是固定的，并且受到限制。 公共预览版中提供的 IoT 连接器配置应提供约200消息/秒的吞吐量。 某种形式的资源容量配置将在正式发布（GA）中提供。

### <a name="what-fhir-version-does-iot-connector-support"></a>IoT 连接器支持哪个 FHIR 版本？
IoT 连接器目前仅支持 FHIR 版本的 R4。 因此，此功能仅适用于 FHIR 的 Azure API 的 R4 实例，Microsoft 目前不打算支持版本 STU3。

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>为什么在 Azure API for FHIR 上启用了 "专用" 链接时无法安装 IoT 连接器？
IoT 连接器目前不支持 "专用链接" 功能。 因此，如果在用于 FHIR 的 Azure API 上启用了专用链接，则无法安装 IoT 连接器，反之亦然。 当 IoT 连接器可用于正式发行版（GA）时，此限制应消失。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>适用于 azure 的开源 IoMT FHIR Connector 与用于 FHIR 服务的 Azure API 的 IoT 连接器功能之间有何区别？
IoT 连接器是适用于 Azure 的开源 IoMT FHIR 连接器的托管和托管版本。 在托管服务中，Microsoft 提供所有维护和更新。

当你运行适用于 Azure 的 IoMT FHIR 连接器时，你可以直接访问基础资源。 不过，如果要存储 PHI 数据，还需要负责维护和更新服务器以及所有必需的符合性工作。

从开发的角度来看，每项功能都首先部署到 Azure 开源 IoMT FHIR 连接器。 在开源中验证后，它将发布到 Azure API for FHIR 服务的 PaaS IoT 连接器功能。 在开源和 PaaS 版本之间进行的时间取决于功能的复杂性以及其他道路地图优先级。

## <a name="next-steps"></a>后续步骤

本文介绍了一些有关用于 FHIR 的 Azure API 的常见问题。 阅读有关 Azure FHIR Server 中支持的功能：
 
>[!div class="nextstepaction"]
>[支持的 FHIR 功能](fhir-features-supported.md)