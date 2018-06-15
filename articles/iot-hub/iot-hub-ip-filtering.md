---
title: Azure IoT 中心 IP 连接筛选器 | Microsoft Docs
description: 如何使用 IP 筛选阻止特定 IP 地址到 Azure IoT 中心的连接。 可阻止来自单独 IP 地址或 IP 地址范围的连接。
author: BeatriceOltean
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: fa44fd21eadb910ce90523b46332505c7303751e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635961"
---
# <a name="use-ip-filters"></a>使用 IP 筛选器

安全性对于基于 Azure IoT 中心的任何 IoT 解决方案来说都是一个重要方面。 作为安全配置的一部分，有时需要显式指定设备可从其连接的 IP 地址。 使用 _IP 筛选器_功能，可以配置规则来拒绝或接受来自特定 IPv4 地址的流量。

## <a name="when-to-use"></a>使用时机

对于需要阻止特定 IP 地址的 IoT 中心终结点的情况，有两个具体用例：

- IoT 中心应仅从指定范围内的 IP 地址接收流量并拒绝任何其他流量。 例如，将 IoT 中心与 [Azure Express Route] 配合使用，以在 IoT 中心与本地基础结构之间创建专用连接。
- 需要拒绝来自 IoT 中心管理员已标识为可疑地址的 IP 地址的流量。

## <a name="how-filter-rules-are-applied"></a>筛选器规则的应用方式

IP 筛选器规则在 IoT 中心服务级别进行应用。 因此，IP 筛选器规则适用于使用任意受支持协议和从设备和后端应用发出的所有连接。

来自与 IoT 中心内的拒绝 IP 规则匹配的 IP 地址的任何连接尝试都将收到未经授权 401 状态代码和说明。 响应消息不会提及 IP 规则。

## <a name="default-setting"></a>默认设置

默认情况下，门户中针对 IoT 中心的“IP 筛选器”网格为空。 此默认设置意味着中心接受来自任何 IP 地址的连接。 此默认设置等效于接受 0.0.0.0/0 IP 地址范围的规则。

![IoT 中心默认 IP 筛选器设置][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>添加或编辑 IP 筛选器规则

添加 IP 筛选器规则时，系统会提示输入以下值：

- “IP 筛选器规则名称”，该名称必须是一个唯一的字母数字字符串，不区分大小写并且最大长度为 128 个字符。 只接受 ASCII 7 位字母数字字符以及以下字符：`{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`。
- 选择“拒绝”或“接受”作为 IP 筛选器规则的“操作”。
- 提供单个 IPv4 地址或者以 CIDR 表示法提供一个 IP 地址块。 例如，在 CIDR 表示法中，192.168.100.0/22 表示从 192.168.100.0 到 192.168.103.255 的 1024 个 IPv4 地址。

![向 IoT 中心添加 IP 筛选规则][img-ip-filter-add-rule]

保存规则后，会看到一个警报，通知你更新正在进行。

![关于保存 IP 筛选规则的通知][img-ip-filter-save-new-rule]

当存在的 IP 筛选规则达到最大数目 10 时，“添加”选项被禁用。

可以通过双击包含某个现有规则的行编辑该规则。

> [!NOTE]
> 拒绝 IP 地址即可阻止其他 Azure 服务（例如门户中的 Azure 流分析、Azure 虚拟机或设备资源管理器）与 IoT 中心交互。

> [!WARNING]
> 如果使用 Azure 流分析 (ASA) 从已启用 IP 筛选的 IoT 中心读取消息，请在 ASA 连接字符串中使用事件中心兼容的名称和 IoT 中心终结点。

## <a name="delete-an-ip-filter-rule"></a>删除 IP 筛选器规则

要删除 IP 筛选器规则，请在网格中选择一个或多个规则，并单击“删除”。

![删除 IoT 中心 IP 筛选规则][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP 筛选器规则评估

IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

例如，如果希望接受 192.168.100.0/22 范围中的地址并拒绝任何其他地址，则网格中的第一个规则应接受 192.168.100.0/22 地址范围。 下一个规则应通过使用 0.0.0.0/0 范围拒绝所有地址。

可以通过单击行开头的三个竖直点并使用拖放操作更改 IP 筛选规则在网格中的顺序。

若要保存新的 IP 筛选器规则顺序，请单击“保存”。

![更改 IoT 中心 IP 筛选规则的顺序][img-ip-filter-rule-order]

## <a name="next-steps"></a>后续步骤

若要进一步探索 IoT 中心的功能，请参阅：

- [操作监视][lnk-monitor]
- [IoT 中心度量值][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
