---
title: 客户数据请求功能
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
ms.openlocfilehash: 28cadac33c4e73e6158f878b3c79ff09b4765fff
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361641"
---
# <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要

Azure IoT 中心设备预配服务是一项基于 REST API 的云服务，其目标是企业客户，可以将设备以无缝、自动且无需人工干预的方式预配到 Azure IoT 中心，确保从设备到云全程安全。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

单台设备由租户管理员分配一个注册 ID 和设备 ID。 来自这些设备的数据以及有关这些设备的数据均基于这些 ID。 Microsoft 不保留任何信息，也无法访问会将这些设备与个人相关联的数据。

在设备预配服务中托管的许多设备（例如办公室调温器或工厂机器人）不是个人设备。 不过，客户可以将某些设备视为可以进行个人标识的设备，并可自行保留将设备与个人绑定的个人资产或库存跟踪方法。 设备预配服务将所有与设备关联的数据视为个人数据来管理和存储。

租户管理员可以使用 Azure 门户或服务的 REST API 来处理信息请求，只需导出或删除与设备 ID 或注册 ID 相关联的数据即可。

> [!NOTE]
> 已经在 Azure IoT 中心通过设备预配服务预配的设备有额外的数据存储在 Azure IoT 中心服务中。 若要针对给定的设备完成一次完整的请求，请查看 [Azure IoT 中心参考文档](../iot-hub/iot-hub-customer-data-requests.md)。

## <a name="deleting-customer-data"></a>删除客户数据

设备预配服务存储登记和注册记录。 登记包含允许预配的设备的相关信息，而注册记录则显示哪些设备已完成预配过程。

租户管理员可以从 Azure 门户中删除登记，这也会删除任何关联的注册记录。

有关详细信息，请参阅[如何管理设备登记](how-to-manage-enrollments.md)。

也可使用 REST API 对登记和注册记录执行删除操作：

* 若要删除单个设备的登记信息，可以使用[设备登记 - 删除](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete)操作。
* 若要删除一组设备的登记信息，可以使用[设备登记组 - 删除](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete)操作。
* 若要删除已预配的设备的相关信息，可以使用[注册状态 - 删除注册状态](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate)操作。

## <a name="exporting-customer-data"></a>导出客户数据

设备预配服务存储登记和注册记录。 登记包含允许预配的设备的相关信息，而注册记录则显示哪些设备已完成预配过程。

租户管理员可以通过 Azure 门户查看登记和注册记录，并可使用复制和粘贴操作将其导出。

若要详细了解如何管理登记，请参阅[如何管理设备登记](how-to-manage-enrollments.md)。

也可使用 REST API 对登记和注册记录执行导出操作：

* 若要导出单个设备的登记信息，可以使用[设备登记 - 获取](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get)操作。
* 若要导出一组设备的登记信息，可以使用[设备登记组 - 获取](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get)操作。
* 若要导出已预配的设备的相关信息，可以使用[注册状态 - 获取注册状态](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate)操作。

> [!NOTE]
> 当你使用 Microsoft 的企业服务时，Microsoft 会生成一些信息，称为系统生成的日志。 租户管理员无法访问或导出设备预配服务的某些系统生成日志。 这些日志包含在服务中执行的实际操作以及与个人设备相关的诊断数据。

## <a name="links-to-additional-documentation"></a>其他文档的链接

设备预配服务 API 的完整文档位于 [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps)。

Azure IoT 中心[客户数据请求功能](../iot-hub/iot-hub-customer-data-requests.md)。