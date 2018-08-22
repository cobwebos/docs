---
title: 如何在 Azure IoT 中心设备预配服务中滚动更新 X.509 证书 | Microsoft Docs
description: 如何使用设备预配服务实例滚动更新 X.509 证书
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 9c73ce159ae7cf5778210e0fb587135f37c73f57
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024614"
---
# <a name="how-to-roll-x509-device-certificates"></a>如何滚动更新 X.509 设备证书

在 IoT 解决方案的生命周期内，需要滚动更新证书。 滚动更新证书的两个主要原因是出现安全违规和证书过期。 

滚动更新证书是一种安全最佳做法，可以在出现违规时帮助保护系统。 作为[假设违规方法](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)的一部分，Microsoft 提倡实施反应式安全流程及预防措施。 这些安全流程应该包括设备证书的滚动更新。 滚动更新证书的频率取决于解决方案的安全需求。 其解决方案涉及到高度敏感数据的客户可以每日滚动更新证书，而其他客户则可以每隔数年滚动更新其证书。

滚动更新设备证书涉及到更新存储在设备和 IoT 中心上的证书。 然后，设备可以使用设备预配服务的一般性[自动预配](concepts-auto-provisioning.md)过程，在 IoT 中心重新预配自身。


## <a name="obtain-new-certificates"></a>获取新证书

可通过多种方法获取 IoT 设备的新证书。 这些方法包括从设备工厂获取证书、生成自己的证书，以及让第三方管理证书的创建。 

证书相互签名，构成了从根 CA 证书到[叶证书](concepts-security.md#end-entity-leaf-certificate)的信任链。 签名证书是用于签署信任链末尾的叶证书的证书。 签名证书可以是根 CA 证书，也可以是信任链中的中间证书。 有关详细信息，请参阅 [X.509 证书](concepts-security.md#x509-certificates)。
 
可通过两种不同的方法获取签名证书。 第一种方法（建议对生产系统使用）是从根证书颁发机构 (CA) 购买签名证书。 这种方法可将安全性向下链接到受信任的源。 

第二种方法是使用 OpenSSL 之类的工具创建自己的 X.509 证书。 此方法非常适合用于测试 X.509 证书，但在安全性方面不能提供很大的保障。 除非你已准备好充当自己的 CA 提供者，否则请只在测试时使用此方法。
 

## <a name="roll-the-certificate-on-the-device"></a>在设备上滚动更新证书

设备上的证书应始终存储在[硬件安全模块 (HSM)](concepts-device.md#hardware-security-module) 等安全位置。 滚动更新设备证书的方式主要取决于它们的创建方式以及在设备中的安装方式。 

如果证书是从第三方获取的，则必须深入了解第三方如何滚动更新其证书。 可以咨询第三方来制定滚动更新流程，或者单独使用他们提供的流程。 

若要管理自己的设备证书，必须构建更新证书的管道。 确保新旧叶证书具有相同的公用名 (CN)。 如果 CN 相同，设备就可以重新预配自身，而无需创建重复的注册记录。


## <a name="roll-the-certificate-in-the-iot-hub"></a>在 IoT 中心滚动更新证书

可以手动将设备证书添加到 IoT 中心。 也可以使用设备预配服务实例自动添加证书。 本文假设使用预配服务实例来支持自动预配。

最初通过自动预配服务预配设备后，它会启动并联系预配服务。 预配服务通过以下方式做出响应：执行标识检查，然后使用设备的叶证书作为凭据，在 IoT 中心创建设备标识。 预配服务随后告知设备它已分配到哪个 IoT 中心，然后，设备使用其叶证书进行身份验证并连接到 IoT 中心。 

在设备上滚动更新新叶证书后，设备不再可以连接到 IoT 中心，因为它使用新证书进行连接。 IoT 中心只会识别具有旧证书的设备。 设备尝试连接会导致“未授权”连接错误。 若要解决此错误，必须更新设备的注册条目，以采用设备的新叶证书。 然后，在重新预配设备时，预配服务可根据需要更新 IoT 中心设备注册表信息。 

如果在预配服务中为设备创建了[注册组](concepts-service.md#enrollment-group)，则建立这种连接可能不会失败。 在这种情况下，如果不是滚动更新设备证书信任链中的根证书或中间证书，并且新证书是注册组中定义的信任链的一部分，则会识别该设备。 如果在对安全违规做出反应时出现这种情况，至少应将组中被视为违规的特定设备证书加入方块列表。 有关详细信息，请参阅[将注册组中的特定设备加入方块列表](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group)。

可在“管理注册”页上更新已滚动更新的证书的注册条目。 若要访问该页，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到包含设备注册条目的 IoT 中心设备预配服务实例。

2. 单击“管理注册”。

    ![管理注册](./media/how-to-roll-certificates/manage-enrollments-portal.png)


如何处理注册条目的更新取决于使用的是单独注册还是组注册。 另外，建议的过程根据滚动更新证书的原因是安全违规还是证书过期而有所不同。 以下部分介绍了如何处理这些更新。


## <a name="individual-enrollments-and-security-breaches"></a>单独注册和安全违规

如果是为了响应安全违规而滚动更新证书，则应使用以下方法来立即删除当前证书：

1. 单击“单独注册”，然后单击列表中的注册 ID 条目。 

2. 单击“删除当前证书”按钮，然后单击文件夹图标选择注册条目的待上传新证书。 完成后单击“保存”。

    如果主要证书和辅助证书同时泄露，应针对这两个证书完成这些步骤。

    ![管理单独注册](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. 从预配服务中删除已泄露的证书后，请导航到 IoT 中心，并删除与已泄露证书相关联的设备注册。     

    ![删除 IoT 中心设备注册](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="individual-enrollments-and-certificate-expiration"></a>单独注册和证书过期

如果滚动更新证书的目的是处理证书过期问题，则应按如下所述使用辅助证书配置，以减少设备尝试预配时造成的停机时间。

以后当辅助证书也即将过期，因此需要滚动更新时，可以换用主要证书配置。 以这种方式在主要证书与辅助证书之间轮换可以减少设备尝试预配时造成的停机时间。


1. 单击“单独注册”，然后单击列表中的注册 ID 条目。 

2. 单击“辅助证书”，然后单击文件夹图标选择注册条目的待上传新证书。 单击“ **保存**”。

    ![使用辅助证书管理单独注册](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. 以后当主要证书过期时，请返回此处，并单击“删除当前证书”按钮删除该主要证书。

## <a name="enrollment-groups-and-security-breaches"></a>注册组和安全违规

若要更新组注册以响应安全违规，应使用以下方法之一来立即删除当前根 CA 证书或中间证书。

#### <a name="update-compromised-root-ca-certificates"></a>更新已泄露的根 CA 证书

1. 单击预配服务实例的“证书”选项卡。

2. 在列表中单击已泄露的证书，然后单击“删除”按钮。 输入证书名称确认删除，然后单击“确定”。 针对所有已泄露的证书重复此过程。

    ![删除根 CA 证书](./media/how-to-roll-certificates/delete-root-cert.png)

3. 遵循[配置已验证的 CA 证书](how-to-verify-certificates.md)中所述的步骤，添加并验证新的根 CA 证书。

4. 单击预配服务实例的“管理注册”选项卡，然后单击“注册组”列表。 在列表中单击自己的注册组名称。

5. 单击“CA 证书”，然后选择新的根 CA 证书。 然后单击“保存”。 

    ![选择新的根 CA 证书](./media/how-to-roll-certificates/select-new-root-cert.png)

6. 从预配服务中删除已泄露的证书后，请导航到包含已泄露设备注册的链接 IoT 中心，并删除与已泄露证书相关联的注册。

    ![删除 IoT 中心设备注册](./media/how-to-roll-certificates/remove-hub-device-registration.png)


#### <a name="update-compromised-intermediate-certificates"></a>更新已泄露的中间证书

1. 单击“注册组”，然后在列表中单击组名称。 

2. 依次单击“中间证书”、“删除当前证书”。 单击文件夹图标，导航到注册组的要上传的新中间证书。 完成后单击“保存”。 如果主要证书和辅助证书同时泄露，应针对这两个证书完成这些步骤。

    此新中间证书应由已添加到预配服务的已验证根 CA 证书签名。 有关详细信息，请参阅 [X.509 证书](concepts-security.md#x509-certificates)。

    ![管理单独注册](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. 从预配服务中删除已泄露的证书后，请导航到包含设备注册的链接 IoT 中心，并删除与已泄露证书相关联的注册。

    ![删除 IoT 中心设备注册](./media/how-to-roll-certificates/remove-hub-device-registration.png)


## <a name="enrollment-groups-and-certificate-expiration"></a>注册组和证书过期

如果滚动更新证书的目的是处理证书过期问题，则应按如下所述使用辅助证书配置，以确保设备尝试预配时不会造成停机。

以后当辅助证书也即将过期，因此需要滚动更新时，可以换用主要证书配置。 以这种方式在主要证书与辅助证书之间轮换可以确保设备尝试预配时不会造成停机。 

#### <a name="update-expiring-root-ca-certificates"></a>更新即将过期的根 CA 证书

1. 遵循[配置已验证的 CA 证书](how-to-verify-certificates.md)中所述的步骤，添加并验证新的根 CA 证书。

2. 单击预配服务实例的“管理注册”选项卡，然后单击“注册组”列表。 在列表中单击自己的注册组名称。

3. 单击“CA 证书”，然后在“辅助证书”配置下选择新的根 CA 证书。 然后单击“保存”。 

    ![选择新的根 CA 证书](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. 以后当主要证书过期时，请单击预配服务实例的“证书”选项卡。 在列表中单击已过期的证书，然后单击“删除”按钮。 输入证书名称确认删除，然后单击“确定”。

    ![删除根 CA 证书](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>更新即将过期的中间证书


1. 单击“注册组”，然后在列表中单击组名称。 

2. 单击“辅助证书”，然后单击文件夹图标选择注册条目的待上传新证书。 单击“ **保存**”。

    此新中间证书应由已添加到预配服务的已验证根 CA 证书签名。 有关详细信息，请参阅 [X.509 证书](concepts-security.md#x509-certificates)。

   ![使用辅助证书管理单独注册](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. 以后当主要证书过期时，请返回此处，并单击“删除当前证书”按钮删除该主要证书。


## <a name="reprovision-the-device"></a>重新预配设备

在设备和设备预配服务上滚动更新证书后，设备可以通过联系设备预配服务来重新预配自身。 

设备重新预配的简单编程方法之一是，当设备在尝试连接到 IoT 中心时如果收到“未授权”错误，使设备联系预配服务以经历预配流。

另一种方法是使新旧证书的有效期保持短时间的重叠，并使用 IoT 中心向设备发送一条命令，让它们通过预配服务重新注册，以更新其 IoT 中心连接信息。 由于每个设备可能以不同的方式处理命令，因此，必须适当地在设备上编程，使其知道在调用命令时要执行哪种操作。 可以使用多种方法来通过 IoT 中心指挥设备，而我们建议使用[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)或[作业](../iot-hub/iot-hub-devguide-jobs.md)来启动该过程。

重新预配完成后，设备可以使用其新证书连接到 IoT 中心。


## <a name="blacklist-certificates"></a>将证书加入方块列表

响应安全违规时，可能需要将设备证书加入方块列表。 若要将设备证书加入方块列表，请禁用目标设备/证书的注册条目。 有关详细信息，请参阅[管理取消注册](how-to-revoke-device-access-portal.md)一文中的“将设备列入方块列表”。

将证书包含为已禁用注册条目的一部分后，使用该证书注册到 IoT 中心的任何尝试都会失败，即使已在另一个注册条目中启用该证书。
 



## <a name="next-steps"></a>后续步骤

- 若要详细了解设备预配服务中的 X.509 证书，请参阅[安全性](concepts-security.md) 
- 若要了解如何使用 Azure IoT 中心设备预配服务执行 X.509 CA 证书的所有权证明，请参阅[如何验证证书](how-to-verify-certificates.md)
- 若要了解如何使用门户创建登记组，请参阅[使用 Azure 门户管理设备登记](how-to-manage-enrollments.md)。










