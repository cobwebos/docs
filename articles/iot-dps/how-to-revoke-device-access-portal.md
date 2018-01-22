---
title: "如何为 Azure IoT 中心设备预配服务管理设备访问 | Microsoft Docs"
description: "如何在 Azure 门户中撤消对 DPS 服务的设备访问权限"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>如何在 Azure 门户中撤消对预配服务的设备访问权限

适当管理设备凭据对于高可见性 IoT 解决方案等系统至关重要。 此类系统的最佳做法是指定明确的计划，规定在设备凭据（SAS 令牌或 X.509 证书）可能被盗用时应如何撤消设备的访问权限。 本文介绍如何在预配时撤消设备访问权限。

若要了解如何在预配设备后撤消对 IoT 中心的设备访问权限， 请参阅[禁用设备](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)。

> [!NOTE] 
> 请注意要撤消访问权限的设备的重试策略。 例如，具有无限的重试策略的设备可能持续尝试注册预配服务，从而消耗服务资源并可能影响性能。

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>通过单独的注册项阻止设备

单独注册适用于单个设备，可使用 X.509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 （使用 SAS 令牌作为证明机制的设备通过单独的注册进行预配。）若要阻止具有单独注册的设备，可禁用或删除其注册项： 

- 若要暂时阻止设备，可禁用其注册项。 

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中，单击要阻止设备访问的预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“单独注册”选项卡。
    4. 单击要阻止的设备的注册项以将其打开。 
    5. 单击注册列表项底部的“禁用”，然后单击“保存”。  

        ![在门户中禁用单独注册](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- 若要永久阻止设备，可删除其注册项。

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中，单击要阻止设备访问的预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“单独注册”选项卡。
    4. 选中要阻止的设备的注册项旁边的选框。 
    5. 单击窗口顶部的“删除”，然后单击“是”以确认删除注册。 

        ![在门户中删除单独注册](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. 完成操作后，会看到该项已从单独注册列表中删除。  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>使用注册组阻止 X.509 中间或根 CA 证书

X.509 证书通常排列在证书信任链中。 如果链中任何阶段的证书被盗用，则信任受损，且必须将该证书入阻止列表了，以防设备预配服务预配包含该证书的任何链中的设备下游。 若要详细了解 X.509 证书和及如何用其预配服务，请参阅 [X.509 证书](./concepts-security.md#x509-certificates)。 

注册组是共享 X.509 证书的常见证明机制的设备的项，由同一个中间或根 CA 签名。 注册组项配置了与中间或根 CA 相关联的 X.509 证书，以及由证书链中包含该证书的设备共享的任何配置值（如孪生状态和 IoT 中心连接）。 若要阻止证书，可禁用或删除其注册组：

- 若要暂时阻止证书，可禁用其注册组。 

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中，单击要阻止签名证书访问的预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“注册组”选项卡。
    4. 单击要阻止的证书的注册组以将其打开。
    5. 单击注册组项左上方的“编辑组”。
    6. 若要禁用注册项，请在“启用项”开关上选择“禁用”，然后单击“保存”。  

        ![在门户中禁用注册组项](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- 若要永久阻止证书，可删除其注册组。

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    2. 从资源列表中，单击要阻止设备访问的预配服务。
    3. 在预配服务中，单击“管理注册”，然后选择“注册组”选项卡。
    4. 选中要阻止的证书的注册组旁边的选框。 
    5. 单击窗口顶部的“删除”，然后单击“是”以确认删除注册组。 

        ![在门户中删除注册组项](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. 完成操作后，会看到该项已从注册组列表中删除。  

> [!NOTE]
> 删除证书注册组后，如果根证书或证书链中上游位置的其他中间证书存在已启用的注册组，则证书链中包含该证书的设备可能仍将能够注册。

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>注册组中阻止特定设备

实现 X.509 证明机制的设备使用设备的证书链和私钥进行身份验证。 如果设备通过设备预配服务进行连接和身份验证，服务将首先查找与设备凭据匹配的单个注册，再搜索注册组以确定是否可以预配设备。 如果服务发现设备对应了已禁用的单个注册，它将阻止设备连接，即使设备的证书链中存在对应于中间或根 CA 的已启用注册组。 若要阻止注册组中的单个设备，请执行以下步骤：

1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
2. 从资源列表中，单击包含要阻止的设备所对应注册组的预配服务。
3. 在预配服务中，单击“管理注册”，然后选择“单独注册”选项卡。
4. 单击顶部的“添加”按钮。 
5. 选择“X.509”作为设备的安全机制，并上传设备证书。 这是设备上安装的已签名最终实体证书，用于生成身份验证证书。
6. 输入设备的“IoT 中心设备 ID”。 
7. 若要禁用注册项，请在“启用项”开关上选择“禁用”。 
8. 单击“ **保存**”。 注册创建成功后，应可在“单独注册”选项卡下看到设备。 

    ![在门户中禁用单独注册](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




