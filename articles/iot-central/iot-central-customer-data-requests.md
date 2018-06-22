---
title: Azure IoT Central 中的客户数据请求功能 | Microsoft Docs
description: Azure IoT Central 中的客户数据请求功能
author: dominicbetts
ms.author: dobett
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: bb5e263b0332f957b4e7f4a928ccd53f639bcd9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629399"
---
# <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要

Azure IoT Central 是完全托管的物联网 (IoT) 软件即服务解决方案，可以方便地用来连接、监视和管理大规模的 IoT 资产，深入分析 IoT 数据，然后采取明智的行动。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>标识客户数据

Azure Active Directory 对象 ID 用于标识用户和分配角色。 Azure IoT Central 门户显示用于角色分配的用户电子邮件地址，但只存储 Azure Active Directory 对象 ID，电子邮件地址是从 Azure Active Directory 动态查询的。 Azure IoT Central 管理员可以在 Azure IoT Central 应用程序的用户管理部分查看、导出和删除应用程序用户。

可以在应用程序中配置接收警报的电子邮件地址。 在这种情况下，电子邮件地址存储在 IoT Central 中，必须从应用内帐户管理页进行管理。

至于设备，Microsoft 不保留其信息，也无法访问将设备与用户相关联的数据。 在 Azure IoT Central 中托管的许多设备（例如售货机或咖啡机）不是个人设备。 不过，客户可以将某些设备视为可以进行个人标识的设备，并可自行保留将设备与个人绑定的个人资产或库存跟踪系统。 Azure IoT Central 将所有与设备关联的数据视为个人数据来管理和存储。

当你使用 Microsoft 企业服务时，Microsoft 会生成一些信息，称为系统生成的日志。 这些日志包含在服务中执行的实际操作以及与个人设备相关（但与用户活动不相关）的诊断数据。 应用程序管理员无法访问或导出 Azure IoT Central 的系统生成日志。

## <a name="deleting-customer-data"></a>删除客户数据

只能通过 IoT Central 管理页删除用户数据。 应用程序管理员可以选择要删除的用户，然后单击应用程序右上角的“删除”来删除记录。 应用程序管理员也可以删除不再与相关应用程序关联的个人帐户。

删除用户后，不会向其发送电子邮件警报。 但是，必须从每个配置的警报中一个个地删除其电子邮件地址。

有关详细信息，请参阅[配置设备的规则和操作](tutorial-configure-rules.md)。

## <a name="exporting-customer-data"></a>导出客户数据

只能通过 IoT Central 管理页导出数据。 可以由应用程序管理员来选择、复制和粘贴客户数据（包括分配的角色）。

## <a name="links-to-additional-documentation"></a>其他文档的链接

如需包括角色定义在内的帐户管理详细信息，请参阅[如何管理应用程序](howto-administer.md)。
