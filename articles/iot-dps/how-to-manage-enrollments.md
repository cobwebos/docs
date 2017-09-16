---
title: "如何管理 Azure IoT 中心的设备注册 | Microsoft Docs"
description: "如何在 Azure 门户中管理 DPS 服务的设备注册"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/06/2017

---

# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>如何管理 IoT 中心设备预配服务中的设备注册

“设备注册”将创建单台设备或一组设备在某一时刻向 Azure IoT 中心设备预配服务进行注册的记录。 注册记录包含注册过程中设备所需的初始配置及所需的 IoT 中心。 本文演示了如何针对预配服务管理设备注册。


## <a name="create-a-device-enrollment"></a>创建设备注册

可通过两种方法向预配服务注册设备：

1. 注册组是共享 X.509 证书的常见证明机制的一组设备的条目，由同一个根 CA 签名。 对于共享所需初始配置的大量设备，或者全部转到同一租户的设备，建议使用注册组。 请注意，只能注册使用 X.509 证明机制作为“注册组”的设备。 

    执行以下步骤可在门户中为一组设备创建注册组。

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中单击要向其注册设备的设备预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“注册组”选项卡。
    4. 单击顶部的“添加”，然后输入注册列表条目所需的信息。 上传此组设备的根证书。 
    5. 单击“保存” 。 注册组创建成功后，应可在“注册组”选项卡下看到组名称。 

        ![门户中的注册组](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. 单独注册是用于单个设备注册的条目。 单独注册可使用 x509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 对于需要唯一初始配置的设备或仅能通过 TPM 或虚拟 TPM 使用 SAS 令牌作为证明机制的设备，建议使用单独注册。 单独注册可能会指定所需 IoT 中心设备 ID。

    可通过以下步骤在门户中创建单独注册。 

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中单击要向其注册设备的设备预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“单独注册”选项卡。
    4. 单击顶部的“添加”按钮。 
    5. 选择该设备的安全机制，并输入注册列表项所需的信息。 如果设备已实现 X.509，请上传签名证书。 
    6. 单击“保存” 。 注册组创建成功后，应可在“单独注册”选项卡下看到设备。 

        ![门户中的单独注册](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>更新注册条目
可通过以下步骤在门户中更新现有注册条目。

1. 在 Azure 门户中打开设备预配服务，然后单击“管理注册”。 
2. 导航到要修改的注册条目。 单击该条目会打开设备注册的摘要信息。 
3. 在此页上，可以修改安全类型和凭据以外的项，例如设备应链接到的 IoT 中心和设备 ID。 还可以修改初始的设备孪生状态。 
4. 完成后，单击“保存”更新设备注册。 

    ![在门户中更新注册](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>删除设备注册
如果不需要将设备预配到任何 IoT 中心，可以通过以下步骤删除门户中的相关注册条目。

1. 在 Azure 门户中打开设备预配服务，然后单击“管理注册”。 
2. 导航到要删除的注册条目并选中。 
3. 单击顶部的“删除”按钮，然后在出现确认提示时选择“是”。 
5. 完成操作后，会看到条目已从设备注册列表中删除。 
 
    ![在门户中删除注册](./media/how-to-manage-enrollments/remove-enrollment.png)




