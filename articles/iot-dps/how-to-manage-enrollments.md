---
title: 通过 Azure 门户管理设备注册 | Microsoft Docs
description: 如何在 Azure 门户中管理 DPS 服务的设备注册
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 5282ee0858c1226b2b743f42203e7b4af9eac6e6
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524119"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>如何通过 Azure 门户管理设备注册

“设备注册”将创建单台设备或一组设备在某一时刻向 Azure IoT 中心设备预配服务进行注册的记录。 注册记录包含注册过程中设备所需的初始配置及所需的 IoT 中心。 本文演示了如何针对预配服务管理设备注册。


## <a name="create-a-device-enrollment"></a>创建设备注册

可通过两种方法向预配服务注册设备：

* “注册组”是共享 X.509 证书共同认证机制的一组设备，由同一个签名证书（可以是[根证书](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate)，也可以是[中间证书](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)）签名，并用于在物理设备上生成设备证书。 对于共享所需初始配置的大量设备，或者全部转到同一租户的设备，建议使用注册组。 请注意，只能注册使用 X.509 证明机制作为“注册组”的设备。 

    执行以下步骤可在门户中为一组设备创建注册组。

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。  
    1. 从资源列表中单击要向其注册设备的设备预配服务。  
    1. 在预配服务中：  
       a. 单击“管理注册”，然后选择“注册组”选项卡。  
       b. 单击顶部的“添加”按钮。  
       c. 当“添加注册组”面板出现时，输入注册列表项的信息。  **组名**是必需的。 另外，对于“证书类型”，请选择“CA 证书”或“中间证书”，并为设备组上传根**主证书**。  
       d. 单击“ **保存**”。 注册组创建成功后，应可在“注册组”选项卡下看到组名称。  

       [![门户中的注册组](./media/how-to-manage-enrollments/group-enrollment.png)]  (./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* 单独注册是用于单个设备注册的条目。 个人注册可使用 x509 证书或 SAS 令牌（来自物理或虚拟 TPM）作为证明机制。 对于需要唯一初始配置的设备或仅能通过 TPM 或虚拟 TPM 使用 SAS 令牌作为证明机制的设备，建议使用单独注册。 单独注册可能会指定所需 IoT 中心设备 ID。

    可通过以下步骤在门户中创建单独注册。

    1. 登录 Azure 门户，单击左侧菜单中的“所有资源”。
    1. 从资源列表中单击要向其注册设备的设备预配服务。
    1. 在预配服务中：  
       a. 单击“管理注册”，然后选择“个人注册”选项卡。  
       b. 单击顶部的“添加”按钮。   
       c. 当“添加注册”面板出现时，输入注册列表项的信息。 首先为设备选择证明**机制**（X.509 或 TPM）。 X.509 证明要求为设备上传叶**主证书**。 TPM 要求为设备输入**证明密钥**和**注册 ID**。  
       d. 单击“ **保存**”。 注册组创建成功后，应可在“单独注册”选项卡下看到设备。  

       [![门户中的个人注册](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>更新注册条目
可通过以下步骤在门户中更新现有注册条目。

1. 在 Azure 门户中打开设备预配服务，然后单击“管理注册”。 
1. 导航到要修改的注册条目。 单击该条目会打开设备注册的摘要信息。 
1. 在此页上，可以修改安全类型和凭据以外的项，例如设备应链接到的 IoT 中心和设备 ID。 还可以修改初始的设备孪生状态。 
1. 完成后，单击“保存”更新设备注册。 

    ![在门户中更新注册](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>删除设备注册
如果不需要将设备预配到任何 IoT 中心，可以通过以下步骤删除门户中的相关注册条目。

1. 在 Azure 门户中打开设备预配服务，然后单击“管理注册”。 
1. 导航到要删除的注册条目并选中。 
1. 单击顶部的“删除”按钮，然后在出现确认提示时选择“是”。 
1. 完成操作后，会看到条目已从设备注册列表中删除。 
 
    ![在门户中删除注册](./media/how-to-manage-enrollments/remove-enrollment.png)


