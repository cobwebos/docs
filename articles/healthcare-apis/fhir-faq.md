---
title: 有关 Azure 中的 FHIR 服务的常见问题解答 - Azure API for FHIR
description: 获取有关 Azure API for FHIR 的常见问题（例如 FHIR API 后面的数据的存储位置以及版本支持）的解答。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269703"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>有关 Azure API for FHIR 的常见问题解答

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

### <a name="what-is-fhir"></a>什么是 FHIR？
 (FHIR 的快速医疗保健互操作性资源 ) 是一个互操作性标准，旨在允许在不同的运行状况系统之间交换医疗保健数据。 此标准由 HL7 组织开发，由世界各地的医疗保健组织使用。 最新版本的 FHIR 是 R4 (版本 4) 。 适用于 FHIR 的 Azure API 支持 R4，并且还支持早期版本 STU3 (Standard for 试用版 3) 。 有关 FHIR 的详细信息，请访问 [HL7.org](http://hl7.org/fhir/summary.html)。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 是否在 Azure 中存储的数据？

是的，数据存储在 Azure 中的托管数据库中。 适用于 FHIR 的 Azure API 不提供对基础数据存储区的直接访问。

### <a name="what-identity-provider-do-you-support"></a>你支持哪些标识提供程序？

目前支持 Microsoft Azure Active Directory 作为标识提供者。

### <a name="what-fhir-version-do-you-support"></a>你支持哪个 FHIR 版本？

我们支持 Azure API for FHIR (PaaS) 和 FHIR Server for Azure (开源) 版本4.0.0 和3.0.1。

有关详细信息，请参阅 [支持的功能](fhir-features-supported.md)。 了解 [HL7 FHIR 的版本历史记录](https://hl7.org/fhir/R4/history.html)中的不同版本之间的更改。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>用于 Azure 的开源 Microsoft FHIR 服务器与用于 FHIR 的 Azure API 之间有何区别？

适用于 FHIR 的 Azure API 是 Azure 的开源 Microsoft FHIR 服务器托管和托管版本。 在托管服务中，Microsoft 提供所有维护和更新。 

当你运行适用于 Azure 的 FHIR 服务器时，你可以直接访问基础服务。 不过，如果要存储 PHI 数据，还需要负责维护和更新服务器以及所有必需的符合性工作。

从开发的角度来看，每项功能都首先部署到开源 Microsoft FHIR Server for Azure。 在开源中验证后，它将发布到 PaaS Azure API for FHIR 解决方案。 在开源和 PaaS 版本之间的时间取决于功能的复杂性以及其他路线图优先级。 

### <a name="what-is-smart-on-fhir"></a>什么是 FHIR 的智能？

智能 (可在 FHIR 上替换医疗应用程序和可重用技术) ，这是一套开放式规范，可将合作伙伴应用程序与 FHIR 服务器和其他运行状况 IT 系统（如电子健康记录和健康信息交换）集成。 通过创建智能的 FHIR 应用程序，你可以确保你的应用程序可以通过不同系统的很多进行访问和利用。
用于 FHIR 的身份验证和 Azure API。 若要了解有关智能的详细信息，请访问 [智能运行状况](https://smarthealthit.org/)。

### <a name="can-i-create-a-custom-fhir-resource"></a>能否创建自定义 FHIR 资源？

我们不允许自定义 FHIR 资源。 如果需要自定义 FHIR 资源，可以在包含扩展的 [基本资源](http://www.hl7.org/fhir/basic.html) 之上构建自定义资源。 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Azure API for FHIR 是否支持 [扩展](https://www.hl7.org/fhir/extensibility.html) ？

我们允许你将任何有效的 FHIR JSON 数据加载到服务器中。 如果要存储定义扩展的结构定义，则可以将其另存为结构定义资源。 当前无法搜索扩展。

### <a name="what-is-the-limit-on-_count"></a>_Count 的限制是多少？

当前计数限制为100。

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>组导出功能是否有任何限制？

对于组导出，我们只导出组中包含的引用，而不是 [组资源](https://www.hl7.org/fhir/group.html)的所有特征。

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>是否可以将捆绑发布到 Azure API for FHIR？

我们目前支持发布 [批处理捆绑包](https://www.hl7.org/fhir/valueset-bundle-type.html) ，但不支持在用于 FHIR 的 Azure API 中发布事务捆绑。 你可以使用由 SQL 支持的开源 FHIR 服务器来发布事务包。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>如何在用于 FHIR 的 Azure API 中获取单个患者的所有资源？

我们支持在用于 FHIR 的 Azure API 中 [搜索隔离舱](https://www.hl7.org/fhir/compartmentdefinition.html) 。 这允许你获取与特定患者相关的所有资源。 请注意，现在的隔离舱包含与患者相关的所有资源，但不包括患者本身，因此，如果您的结果中需要患者资源，还需要搜索以获取患者。

下面是此操作的一些示例：

* 获取患者/<id>/*
* 获取患者/ <id> /Observation
* 获取患者/ <id> /Observation？代码 = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>在何处可查看在工作流中使用 Azure API for FHIR 的一些示例？

我们有一系列参考体系结构，它在 [运行状况体系结构 GitHub 页](https://github.com/microsoft/health-architectures)上提供。

## <a name="azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）

### <a name="what-is-iomt"></a>什么是 IoMT？
IoMT 代表医疗网，它是一类 IoT 设备，可通过网络与其他医疗保健 IT 系统一起捕获和交换健康和 wellness 数据。 IoMT 设备的一些示例包括健身和临床可穿戴设备、监视传感器、活动跟踪器、护理网亭甚至智能药丸。

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>需要多少 Azure IoT Connector for FHIR (预览版) ？
单个 Azure IoT Connector for FHIR * 可用于从大量不同类型的设备中引入数据。 由于以下原因，你可能仍会决定使用不同的连接器：
- **规模**：对于公共预览版，适用于 FHIR 资源容量的 Azure IoT 连接器是固定的，应提供约200消息/秒的吞吐量。 如果需要更高的吞吐量，可以为 FHIR 添加更多 Azure IoT 连接器。
- **设备类型**：对于设备管理原因，你可以为 FHIR 的每种类型的 IoMT 设备设置单独的 Azure IoT 连接器。

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>在公共预览版期间，适用于 FHIR (preview) 的 Azure IoT 连接器数量是否有限制？
是的，当功能处于公共预览时，只能为每个订阅创建两个适用于 FHIR 的 Azure IoT 连接器。 存在此限制是为了防止意外支出，因为在预览期间，该功能可免费使用。 请求时，最多可将此限制提升到最多5个适用于 FHIR 的 Azure IoT 连接器。

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>公共预览版期间，哪些 Azure 区域的适用于 FHIR 的 Azure IoT 连接器 (预览版) 功能可用？
适用于 FHIR 的 azure IoT 连接器适用于适用于 FHIR 的 Azure API 的所有 Azure 区域。

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>能否为适用于 FHIR 的 Azure IoT Connector 配置扩展容量 (预览) ？
由于适用于 FHIR 的 Azure IoT 连接器在公共预览版期间免费，因此其缩放容量是固定的，并受到限制。 公共预览版中提供的适用于 FHIR 配置的 Azure IoT 连接器应提供约200消息/秒的吞吐量。 某些形式的资源容量配置将在公开上市 (公开) 中可用。

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>适用于 FHIR 的 FHIR 版本 Azure IoT 连接器 (预览版) 支持？
适用于 FHIR 的 Azure IoT 连接器目前仅支持 FHIR 版本 R4。 因此，此功能仅适用于 FHIR 的 Azure API 的 R4 实例，Microsoft 目前不打算支持版本 STU3。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>为什么在 Azure API for FHIR 上启用了 "专用" 链接时，无法安装适用于 FHIR 的 Azure IoT 连接器 (预览) ？
Azure IoT Connector for FHIR 目前不支持私有链接功能。 因此，如果在用于 FHIR 的 Azure API 上启用了 Private Link，则无法为 FHIR 安装 Azure IoT 连接器，反之亦然。 当 Azure IoT Connector for FHIR 适用于公开上市 (GA) 时，此限制应消失。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>适用于 Azure 的开源 IoMT FHIR 连接器与用于 FHIR (预览版的 azure IoT 连接器之间的区别) 用于 FHIR 服务的 Azure API 的功能？
适用于 FHIR 的 azure IoT 连接器是适用于 Azure 的开源 IoMT FHIR 连接器的托管和托管版本。 在托管服务中，Microsoft 提供所有维护和更新。

当你运行适用于 Azure 的 IoMT FHIR 连接器时，你可以直接访问基础资源。 不过，如果要存储 PHI 数据，还需要负责维护和更新服务器以及所有必需的符合性工作。

从开发的角度来看，每项功能都首先部署到 Azure 开源 IoMT FHIR 连接器。 在开源中验证后，它将发布到适用于 FHIR 服务的 Azure API 的 PaaS Azure IoT 连接器 FHIR 功能。 在开源和 PaaS 版本之间进行的时间取决于功能的复杂性以及其他道路地图优先级。

## <a name="next-steps"></a>后续步骤

本文介绍了一些有关用于 FHIR 的 Azure API 的常见问题。 阅读有关 Azure FHIR Server 中支持的功能：
 
>[!div class="nextstepaction"]
>[支持的 FHIR 功能](fhir-features-supported.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。

FHIR 是 HL7 的注册商标，经 HL7 许可使用。