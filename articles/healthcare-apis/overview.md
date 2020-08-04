---
title: 什么是 Azure API for FHIR？ - Azure API for FHIR
description: 使用 Azure API for FHIR，可通过 FHIR API 快速交换数据。 使用托管的云服务引入、管理和保存受保护健康信息 (PHI)。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 72e199e45047e1b425b2587c6b4028efb84060df
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087435"
---
# <a name="what-is-azure-api-for-fhirreg"></a>什么是 Azure API for FHIR&reg;？

Azure API for FHIR 由云中托管的平台即服务 (PaaS) 产品/服务提供支持，它通过快速医疗保健互操作性资源 (FHIR®) API 实现快速交换数据。 借助于它，使用健康数据的任何人都可以更轻松地在云中引入、管理和保存受保护健康信息 ([PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html))： 

- 托管的 FHIR 服务，可在数分钟内在云中进行预配 
- Azure 中基于 FHIR® 的企业级终结点，用于数据访问和 FHIR® 格式的存储
- 高性能、低延迟
- 在兼容的云环境中安全管理受保护健康数据 (PHI)
- 用于移动和 Web 实现的 SMART on FHIR
- 使用基于角色的访问控制 (RBAC) 大规模控制自己的数据
- 对每个数据存储中的访问、创建、修改和读取进行审核日志跟踪

使用 Azure API for FHIR，可在几分钟内创建和部署 FHIR 服务，从而利用云的弹性缩放。  只需为所需的吞吐量和存储付费。 支持 Azure API for FHIR 的 Azure 服务旨在实现快速性能（无论管理的数据集大小如何）。

使用 FHIR API 和兼容的数据存储，可以安全地连接任何使用 FHIR API 的系统，并与之进行交互。  Microsoft 承担 PaaS 产品/服务中的操作、维护、更新和合规性要求，使你可以释放自己的操作和开发资源。 

下面的视频概述了 Azure API for FHIR：

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>使用 FHIR 发挥数据功能

医疗保健行业正迅速将健康数据转换为 [FHIR&reg;](https://hl7.org/fhir)（快速医疗保健互操作性资源）这一新兴标准。 FHIR 实现了具有标准化语义和数据交换的强大可扩展数据模型，可让使用 FHIR 的所有系统协同工作。  通过将数据转换为 FHIR，可以快速连接现有数据源，如电子健康记录系统或研究数据库。 FHIR 还支持在移动和 Web 开发的新式实现中快速交换数据。 最重要的是，FHIR 可以通过分析和机器学习工具简化数据引入并加速开发。  

### <a name="securely-manage-health-data-in-the-cloud"></a>在云中安全地管理健康数据

借助 Azure API for FHIR，可通过基于 HL7 FHIR 规范的一致 RESTful FHIR API 进行数据交换。 在 Azure 中的托管 PaaS 产品/服务的支持下，它还提供了可缩放的和安全的环境，用于以本机 FHIR 格式管理和存储受保护健康信息 (PHI) 数据。  

### <a name="free-up-your-resources-to-innovate"></a>释放资源进行创新

你可以投入资源来构建并运行自己的 FHIR 服务，但使用 Azure API for FHIR，Microsoft 可承担操作、维护、更新和合规性要求的工作负载，使你能够释放自己的操作和开发资源。

### <a name="enable-interoperability-with-fhir"></a>实现与 FHIR 的互操作性

通过使用 Azure API for FHIR，可以连接利用 FHIR API 执行读取、写入、搜索和其他功能的任何系统。  它可以用作一种功能强大的工具，利用来自电子健康记录、临床医生和患者仪表板、远程监控程序的临床数据或系统外部具有 FHIR API 的数据库来整合、规范化和应用机器学习。

### <a name="control-data-access-at-scale"></a>大规模控制数据访问

你可以控制你的数据。 使用基于角色的访问控制 (RBAC)，可管理存储和访问数据的方式。  为了提高安全性并减少管理工作负载，可以根据为环境创建的角色定义来确定谁有权访问创建的数据集。  

### <a name="audit-logs-and-tracking"></a>审核日志和跟踪 

利用内置的审核日志快速跟踪数据的位置。 跟踪每个数据存储中的访问、创建、修改和读取。

### <a name="secure-your-data"></a>保护数据

通过无与伦比的安全智能保护 PHI。  数据被隔离到每个 API 实例的唯一数据库中，并通过多区域故障转移进行保护。 Azure API for FHIR 针对数据实现了一个分层的深度防御和高级威胁防护。  

## <a name="applications-for-a-fhir-service"></a>FHIR 服务的应用程序

FHIR 服务器是实现健康数据互操作性的关键工具。  Azure API for FHIR 设计为可以快速创建、部署和开始使用的 API 和服务。  随着 FHIR 标准在医疗保健领域的扩展，用例将继续增长；下面是一些初始客户应用场景，Azure API for FHIR 在这些场景下非常有用： 

- **初创公司/IOT 和应用开发：** 开发以患者或提供者为中心的应用（移动或 Web）的客户可以将 Azure API for FHIR 用作完全托管的后端服务。 Azure API for FHIR 提供了宝贵的资源，这表现在客户可以在为健康数据而设计的安全云环境中管理数据和交换数据、利用 SMART on FHIR 实现指南，并使其技术能够被所有提供商系统所利用（例如，大多数 EHR 已启用 FHIR 读取 API）。   
- **医疗保健生态系统：** 尽管 EHR 在许多临床环境中作为主要的“真实数据来源”而存在，但提供者拥有多个未相互连接的数据库或以不同格式存储数据的情况并不少见。  通过将 Azure API for FHIR 用作一项基于这些系统的服务，你能够以 FHIR 格式对数据进行规范化。  这有助于以一致的数据格式实现跨多个系统的数据交换。 

- **研究：** 医疗保健研究人员会发现 FHIR 标准总体上是有用的，且 Azure API for FHIR 也是有用的，因为它围绕着常见的 FHIR 数据模型规范化数据，并减少机器学习和数据共享的工作负载。
通过 Azure API for FHIR 进行数据交换可提供审核日志和访问控制，从而帮助控制数据流以及谁有权访问哪些数据类型。 

## <a name="fhir-from-microsoft"></a>来自 Microsoft 的 FHIR

Microsoft 提供的 FHIR 功能有两种配置：

* Azure API for FHIR - Azure 中的 PaaS 产品/服务，可以在 Azure 门户中轻松预配并由 Microsoft 管理。
* 适用于 Azure 的 FHIR 服务器 - 可部署到 Azure 订阅中的开放源代码项目，可在 GitHub 上的 https://github.com/Microsoft/fhir-server 获得。

对于需要扩展或自定义 FHIR 服务器或需要访问基础服务（例如数据库）而无需通过 FHIR API 的用例，开发人员应选择适用于 Azure 的开放源代码 FHIR 服务器。   若要实现统包式生产就绪 FHIR API 和后端服务（其中持久化数据应只通过 FHIR API 进行访问），开发人员应选择 Azure API for FHIR

## <a name="iot-connector-preview"></a>IoT 连接器（预览版）

IoT 连接器是 Azure API for FHIR 的一项可选功能，使你能够引入来自医疗物联网 (IoMT) 设备的数据。 医疗物联网是一类 IoT 设备，用于通过网络捕获健康状况数据并与其他医疗保健 IT 系统交换此类数据。 IoMT 设备的一些示例包括健身和临床可穿戴设备、监视传感器、活动跟踪器、护理网亭甚至智能药丸。 借助 IoT 连接器功能，你可以快速设置服务，以可缩放、安全且合规的方式将 IoMT 数据引入到 Azure API for FHIR 中。

IoT 连接器可以接受由 IoMT 设备发出的任何基于 JSON 的消息。 此数据首先转换为基于 FHIR 的适当[观察](https://www.hl7.org/fhir/observation.html)资源，然后持久保存到 Azure API for FHIR 中。 数据转换逻辑是通过基于消息架构和 FHIR 要求配置的一对映射模板定义的。 设备数据可以直接推送到 IoT 连接器，也可以与其他 Azure IoT 解决方案（[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)和 [Azure IoT Central](https://docs.microsoft.com/azure/iot-central/)）无缝结合使用。 IoT 连接器提供了安全的数据管道，同时允许 Azure IoT 解决方案管理物理设备的预配和维护。

### <a name="applications-of-iot-connector-preview"></a>IoT 连接器的应用（预览版）

医疗保健领域越来越广泛地使用 IoMT 设备，而 IoT 连接器旨在弥合将具有安全性和合规性的多设备数据引入 Azure API for FHIR 的鸿沟。 将 IoMT 数据引入 FHIR 服务器可实现全面的数据见解和创新的临床工作流。 IoT 连接器的一些常见使用场景包括：
- **远程患者监视/远程医疗：** 远程患者监视提供了在传统医疗场所之外收集患者健康状况数据的功能。 医疗保健机构可以使用 IoT 连接器将远程设备生成的健康状况数据引入 Azure API for FHIR。 此类数据可用于密切跟踪患者的健康状况、监视患者遵守治疗方案的情况并提供个性化护理。
- **研究与生命科学：** 临床试验正在大范围采用 IoMT 设备（例如生物传感器、可穿戴设备、移动应用）来捕获试验数据。 这些试验可以利用 IoT 连接器以安全、高效且有效的方式将设备数据传输到 Azure API for FHIR。 试验数据传输到 Azure API for FHIR 后，便可对其进行实时分析。
- **高级分析：** IoMT 设备可以快速提供各类海量数据，这使它非常适合为机器学习模型提供训练和测试数据。 从本质上来说，IoT 连接器是针对各种数据频率、灵活的数据模式和低延迟的云缩放而构建的。 这些属性使 IoT 连接器成为捕获设备数据以满足高级分析需求的极佳选择。
- **智能医院/诊所：** 如今，智能医院和诊所正在建立互连数字资产的基础结构。 IoT 连接器可用于捕获和集成来自这些连接组件的数据。 来自此类数据集的可行见解可实现更好的患者护理并提高运营效率。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure API for FHIR，请遵循 5 分钟的快速入门来部署 Azure API for FHIR。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)

若要试用 IoT 连接器功能，请参阅使用 Azure 门户部署 IoT 连接器的快速入门。

>[!div class="nextstepaction"]
>[部署 IoT 连接器](iot-fhir-portal-quickstart.md)


FHIR 是 HL7 的注册商标，经 HL7 许可使用。
