---
title: "如何取消预配使用 Azure IoT 中心设备预配服务登记的设备 | Microsoft Docs"
description: "如何在 Azure 门户中取消预配 DPS 服务登记的设备"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 83ed72c0f2eb342c372ef97e5443d60eab1e2174
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>如何取消预配由预配服务登记的设备

有时，我们可能需要取消预配通过设备预配服务预配的设备。 例如，设备已转售、移到不同的 IoT 中心、丢失、被盗或者信息泄露。 

一般情况下，取消预配设备涉及到两个步骤：

1. 撤销设备对预配服务的访问权限。 根据是要暂时还是永久撤销访问权限，或者是否使用了 X.509 认证机制，在现有登记组的层次结构中，可能需要禁用或删除登记条目。 
 
   - 若要了解如何使用门户撤销设备访问权限，请参阅[撤销设备访问权限](how-to-revoke-device-access-portal.md)。
   - 若要了解如何使用某个预配服务 SDK 以编程方式撤销设备访问权限，请参阅[使用服务 SDK 管理设备登记](how-to-manage-enrollments-sdks.md)。

2. 在预配设备的 IoT 中心的标识注册表中禁用或删除该设备的条目。 有关详细信息，请参阅 Azure IoT 中心文档中的[管理设备标识](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)。 

取消预配设备所要执行的确切步骤取决于该设备使用的认证机制。

结合自签名叶证书（无证书链）使用 TPM 认证或 X.509 认证的设备是通过单独的登记条目预配的。 对于这些设备，可以删除该条目以永久撤销设备对预配服务的访问权限，或禁用该条目以暂时撤销其访问权限，然后在设备预配到的 IoT 中心的标识注册表中删除或禁用该设备。

使用 X.509 认证时，也可以通过登记组预配设备。 登记组中配置了一个签名证书（中间证书或根 CA 证书），可以使用其证书链中的该证书来控制设备对预配服务的访问。 若要详细了解登记组以及预配服务中的 X.509 证书，请参阅 [X.509 证书](concepts-security.md#x509-certificates)。 

若要查看已通过某个登记组预配的设备列表，可以查看该登记组的详细信息。 这样，便可以轻松了解每个设备已预配到哪个 IoT 中心。 查看设备列表： 

1. 登录到 Azure 门户，单击左侧菜单中的“所有资源”。
2. 在资源列表中单击预配服务。
3. 在预配服务中，单击“管理注册”，然后选择“注册组”选项卡。
4. 单击登记组将其打开。

   ![在门户中查看登记组条目](./media/how-to-unprovision-devices/view-enrollment-group.png)

使用登记组时，需要考虑两种情况：

- 若要取消预配所有已通过登记组预配的设备，必须先禁用该登记组，以将其签名证书加入方块列表。 然后，可以使用该登记组的预配设备列表，从每个设备的相应 IoT 中心的标识注册表中禁用或删除该设备。 从相应的 IoT 中心禁用或删除所有设备后，可以选择删除登记组。 但请注意，如果删除登记组，并且在一个或多个设备的证书链中为某个级别较高的签名证书启用了登记组，则这些设备可能重新登记。 
- 若要从登记组中取消预配单个设备，必须先为其叶（设备）证书创建一个已禁用的单独登记。 这会撤销该设备对预配服务的访问权限，同时仍允许链中具有登记组签名证书的其他设备访问。 然后，可以使用登记组详细信息中的预配设备列表来查找设备已预配到的 IoT 中心，并从该中心的标识注册表中禁用或删除该设备。 不要删除设备的已禁用单独登记。 否则，设备会通过登记组重新登记。 










