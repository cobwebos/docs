---
title: "StorSimple 8000 系列安全性 | Microsoft 文档"
description: "介绍保护 StorSimple 服务、设备以及本地数据和云中数据的安全和隐私功能。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: c14927f82ca01320206ccec83216777b7d1b8708
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple 安全性和数据保护

## <a name="overview"></a>概述

安全性是任何采用新技术（尤其是在该技术用于处理机密或专有数据时）的人关心的主要问题。 评估不同技术时，必须考虑在数据保护方面增加的风险和成本。 Microsoft Azure StorSimple 为数据保护提供安全和隐私解决方案，可确保以下方面：

* **保密性** – 只有经过授权的实体可以查看数据。
* **完整性** - 只有经过授权的实体可以修改或删除数据。

Microsoft Azure StorSimple 解决方案由四个彼此交互的主要组件组成：

* **Microsoft Azure 中托管的 StorSimple 设备管理器服务** – 用于配置和预配 StorSimple 设备的管理服务。
* **StorSimple 设备** – 数据中心内安装的物理设备。 所有生成数据的主机和客户端都连接到 StorSimple 设备，而该设备管理这些数据，并根据需要将数据移到 Azure 云。
* **连接到设备的客户端/主机** – 基础结构中连接到 StorSimple 设备并生成需要保护的数据的客户端。
* **云存储** – Azure 云中存储数据的位置。

以下各节介绍可帮助保护上述每个组件和每个组件上存储的数据的 StorSimple 安全功能。 它还包括一组你可能对 Microsoft Azure StorSimple 安全性持有的问题和相应的解答。

## <a name="storsimple-device-manager-service-protection"></a>StorSimple 设备管理器服务保护

StorSimple 设备管理器服务是 Microsoft Azure 中托管的用于管理组织已采购的所有 StorSimple 设备的管理服务。 可以通过使用组织凭据通过 Web 浏览器登录到 Azure 门户来访问 StorSimple 设备管理器服务。

访问 StorSimple 设备管理器服务需要组织具备包含 StorSimple 在内的 Azure 订阅。 订阅决定可以在 Azure 门户中访问的功能。 如果组织没有 Azure 订阅，并且你想要了解有关 Azure 订阅的详细信息，请参阅[以组织名义注册 Azure](../active-directory/sign-up-organization.md)。

由于 StorSimple 设备管理器服务在 Azure 中托管，因此它受 Azure 安全功能保护。 有关 Microsoft Azure 提供的安全功能的详细信息，请转到 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)。

## <a name="storsimple-device-protection"></a>StorSimple 设备保护

StorSimple 设备一个本地混合存储设备，包含固态硬盘 (SSD) 和硬盘驱动器 (HDD)，还具有冗余控制器和自动故障转移功能。 控制器管理存储分层，将当前使用的（或热）数据放在本地存储（在 StorSimple 设备或本地服务器中）中，而将不经常使用的数据移到云中。

只允许经过授权的 StorSimple 设备加入 Azure 订阅中创建的 StorSimple 设备管理器服务。 若要授权某个设备，必须通过提供服务注册密钥将其注册到 StorSimple 设备管理器服务中。 服务注册密钥是在 Azure 门户中生成的 128 位随机密钥。

![服务注册密钥](./media/storsimple-security/ServiceRegistrationKey.png)

若要了解如何获取服务注册密钥，请转到[步骤 2：获取服务注册密钥](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)。

服务注册密钥是包含 100 多个字符的长密钥。 可以复制密钥，并将其保存到位于安全位置的文本文件中，以便可以使用它根据需要授权其他设备。 如果服务注册密钥在注册第一台设备后丢失，可以从 StorSimple 设备管理器服务生成新密钥。 这不会影响现有设备的操作。

注册设备后，该设备将使用令牌与 Microsoft Azure 进行通信。 在设备注册后不使用服务注册密钥。

> [!NOTE]
> 我们建议在每次使用服务注册密钥后，重新生成服务注册密钥。


## <a name="protect-your-storsimple-solution-via-passwords"></a>通过密码保护 StorSimple 解决方案

密码是计算机安全性的一个重要方面，广泛应用于 StorSimple 解决方案中以帮助确保只有经过授权的用户才能访问数据。 StorSimple 允许配置以下密码：

* StorSimple 设备管理员密码
* 质询握手身份验证协议 (CHAP) 发起程序和目标密码
* StorSimple 快照管理器密码

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell for StorSimple 和 StorSimple 设备管理员密码

Windows PowerShell for StorSimple 是一个可用于管理 StorSimple 设备的命令行界面。 Windows PowerShell for StorSimple 的功能让你能够执行以下操作：注册设备、配置设备上的网络接口、安装特定类型的更新、通过访问支持会话对设备进行故障排除，以及更改设备状态。 可以通过连接到设备上的串行控制台或通过使用 Windows PowerShell 远程处理来访问 Windows PowerShell for StorSimple。

可以通过 HTTPS 或 HTTP 完成 PowerShell 远程处理。 如果启用通过 HTTPS 的远程管理，必须从设备下载远程管理证书，并将其安装在远程客户端上。 有关 PowerShell 远程处理的详细信息，请转到 [远程连接到 StorSimple 设备](storsimple-8000-remote-connect.md)。

使用 Windows PowerShell for StorSimple 连接到设备后，需要提供设备管理员密码，以便登录到设备。

![设备管理员密码](./media/storsimple-security/DeviceAdminPW.png)

牢记以下最佳实践：

* 默认情况下，远程管理功能处于关闭状态。 可以使用 StorSimple 设备管理器服务启用它。 最佳安全做法是仅在实际需要的时间段才启用远程访问。
* 如果更改密码，请务必通知所有远程访问用户，使他们不会遇到意外的连接丢失。
* StorSimple 设备管理器服务无法检索现有密码：它只能重置这些密码。 我们建议将所有密码都存储在安全位置，以便在忘记密码时不必重置密码。 如果确实需要重置密码，请务必在重置密码前通知所有用户。

可以使用与设备的串行连接来访问 Windows PowerShell 界面。 还可以使用 HTTP 或 HTTPS 进行远程访问，后者提供更高的安全性。 HTTPS 提供比串行连接或 HTTP 连接更高级别的安全性。 但是，要使用 HTTPS，必须先将证书安装到要访问设备的客户端计算机上。 可以从 StorSimple 设备管理器服务中的设备配置页下载远程访问证书。 如果远程访问证书丢失，则必须下载新证书并将其传播到所有有权使用远程管理的客户端上。

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>质询握手身份验证协议 (CHAP) 发起程序和目标密码

CHAP 是 StorSimple 设备用于验证远程客户端身份的身份验证方案。 该验证基于共享密码。 CHAP 可以是单向或相互（双向）的。 在单向 CHAP 中，目标（StorSimple 设备）对发起程序（主机）进行身份验证。 相互或反向 CHAP 则要求目标对发起程序进行身份验证，再由发起程序对目标进行身份验证。 Azure StorSimple 可配置为使用任一种方法。

配置 CHAP 时请注意以下事项：

* CHAP 用户名包含的字符必须少于 233 个。
* CHAP 密码必须介于 12 到 16 个字符之间。 试图使用更长的用户名或密码将导致在 Windows 主机上进行身份验证失败。
* 不能将同一密码同时用于 CHAP 发起程序和 CHAP 目标。
* 设置密码之后，密码可以更改但不能检索。 如果更改了密码，请务必通知所有远程访问用户，以便他们能够成功连接到 StorSimple 设备。

有关 CHAP 及如何为 StorSimple 解决方案配置 CHAP 的详细信息，请转至[为 StorSimple 设备配置 CHAP](storsimple-8000-configure-chap.md)。

### <a name="storsimple-snapshot-manager-password"></a>StorSimple 快照管理器密码

StorSimple 快照管理器是一个 Microsoft 管理控制台 (MMC) 管理单元，它使用卷组和 Windows 卷影复制服务来生成应用程序一致性备份。 此外，还可以使用 StorSimple 快照管理器来创建备份计划，并克隆或还原卷。

将设备配置为使用 StorSimple Snapshot Manager 时，需要提供 StorSimple Snapshot Manager 密码。 此密码最初在注册过程中在 Windows PowerShell for StorSimple 中设置。 此密码还可从 StorSimple 设备管理器服务设置和更改。 此密码通过 StorSimple 快照管理器对设备进行身份验证。

![StorSimple 快照管理器密码](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple Snapshot Manager 密码必须为 14 到 15 个字符，必须包含 3 个或更多大写字符、小写字符、数字字符和特殊字符的组合。 设置 StorSimple 快照管理器密码之后，密码可以更改但不能检索。 如果更改了密码，请务必通知所有远程用户。

有关 StorSimple Snapshot Manager 的详细信息，请转至[什么是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>密码最佳实践

我们建议使用以下准则来帮助确保 StorSimple 密码具有很高强度并受到严密保护：

* 每三个月更改一次密码。 每年会强制更改密码。
* 使用强密码。 有关详细信息，请转到[创建更强密码并对其进行保护](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/)。
* 对于不同的访问机制，应始终使用不同的密码；你指定的每个密码应该是唯一的。
* 不要与未被授权访问 StorSimple 设备的任何人共享密码。
* 不要在任何人面前说出密码或提示密码格式。
* 如果怀疑某个帐户或密码已受到安全威胁，请向信息安全部门报告此事件。
* 请将所有密码视为敏感机密信息处理。 

## <a name="storsimple-data-protection"></a>StorSimple 数据保护

本节介绍保护传输中数据和存储数据的 StorSimple 安全功能。

如其他各节所述，在用户能够获取对 StorSimple 解决方案的访问权限之前，可使用密码对他们进行授权和身份验证。 另外一个安全注意事项是，当数据在存储系统之间传输或正在存储时，要保护数据以防止未授权用户访问。 以下各节介绍 StorSimple 提供的数据保护功能。

> [!NOTE]
> 删除重复可为 StorSimple 设备上和 Microsoft Azure 存储中存储的数据提供附加保护。 对数据删除重复时，数据对象与用于映射和访问它们的元数据分开存储：没有可基于卷结构、文件系统或文件名重新构造数据的可用存储级别上下文。


## <a name="protect-data-flowing-through-the-service"></a>保护流经服务的数据

StorSimple 设备管理器服务的主要作用是管理和配置 StorSimple 设备。 StorSimple 设备管理器服务在 Microsoft Azure 中运行。 可以使用 Azure 门户来输入设备配置数据，然后 Microsoft Azure 使用 StorSimple 设备管理器服务将数据发送到设备。 StorSimple 使用非对称密钥对系统，帮助确保 Azure 服务的安全威胁不会导致存储的信息受到安全威胁。

![动态数据加密](./media/storsimple-security/DataEncryption.png)

非对称密钥系统通过以下方式帮助保护流经服务的数据：

1. 使用非对称公钥和私钥对的数据加密证书在设备上生成，用于保护数据。 这些密钥在注册第一个设备时生成。
2. 数据加密证书密钥导出到受服务数据加密密钥保护的个人信息交换 (.pfx) 文件，该密钥是在注册过程中由首个设备随机生成的 128 位强密钥。
3. 证书的公钥被安全地提供给 StorSimple 设备管理器服务，私钥则保留在设备上。
4. 进入服务的数据使用公钥加密，并使用存储在设备上的私钥解密，从而确保 Azure 服务无法解密流向设备的数据。

仅在向服务注册的第一个设备上生成服务数据加密密钥。 向服务注册的所有后续设备必须使用同一服务数据加密密钥。

> [!IMPORTANT]
> 务必要生成该服务数据加密密钥的副本并将其保存在安全位置。 服务数据加密密钥的副本应以这种方式存储：可以由经过授权的人员访问，并可以轻松地传递给设备管理员。
> 
> 如果服务数据加密密钥丢失，Microsoft 支持人员可以帮助你检索该密钥，前提是必须至少有一个设备处于联机状态。 我们建议在检索到服务数据加密密钥后对其进行更改。

若要更改服务数据加密密钥和相应的数据加密证书，请按照[更改 StorSimple 设备管理器服务的服务数据加密密钥](storsimple-8000-manage-service.md#change-the-service-data-encryption-key) 中的步骤进行操作。 更改加密密钥需要使用新密钥更新所有设备。 因此，我们建议在所有设备都处于联机状态时更改该密钥。 如果设备处于脱机状态，则可以在不同时间更改其密钥。 使用过期密钥的设备仍能够运行备份，但它们在更新密钥前将无法还原数据。

服务数据加密密钥和数据加密证书不会过期。 但是，我们建议每年更改一次服务数据加密密钥以帮助防止密钥泄漏。

## <a name="protect-data-at-rest"></a>保护静止的数据

StorSimple 设备管理数据的方法是根据它们的使用频率，将它们存储在本地和云中的多个层中。 连接到设备的所有主机都将数据发送到设备，并设备根据需要将数据移到云中。 数据通过 Internet 从设备安全传输到云。 每个设备都有一个 iSCSI 目标，它会显示该设备上的所有共享卷。 所有数据在发送到云存储之前都进行了加密。 

![云存储加密密钥](./media/storsimple-security/CloudStorageEncryption.png)

为了确保移动到云的数据的安全性和完整性，StorSimple 允许按照以下方式定义云存储加密密钥：

* 创建卷容器时，可以指定云存储加密密钥。 该密钥以后不能修改或添加。
* 卷容器中的所有卷都共享同一加密密钥。 如果希望对特定卷使用不同加密方式，我们建议创建新的卷容器以承载该卷。
* 在 StorSimple 设备管理器服务中输入云存储加密密钥时，该密钥会使用服务数据加密密钥的公共部分进行加密，然后发送到设备。
* 云存储加密密钥不会存储在服务中的任何位置，只有设备才知道该密钥。
* 指定云存储加密密钥是可选操作。 可将已在主机上加密的数据发送到设备。

### <a name="additional-security-best-practices"></a>其他安全性最佳实践

* 分离流量：通过部署完全分离的网络并使用物理隔离非可选项的 VLAN 将 iSCSI SAN 和企业局域网上的用户流量隔离。 iSCSI 存储器的专用网络将保障业务关键数据的安全性和性能。 不建议将存储器和企业局域网上的用户流量混合，这样会增加滞后时间并可能造成网络故障。
* 对于主机端网络安全，请使用支持 TCP/IP Offload Engine (TOE) 的网络接口。 TOE 通过处理网络适配器上的 TCP 减少 CPU 负荷。

## <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

每个 Microsoft Azure 订阅都可以创建一个或多个存储帐户。 （存储帐户提供唯一命名空间，方便使用存储在 Azure 云中的数据。）对存储帐户的访问由订阅以及与该存储帐户关联的访问密钥控制。

创建存储帐户时，Microsoft Azure 生成两个 512 位存储访问密钥，其中一个用于在 StorSimple 设备访问存储帐户时进行身份验证。 请注意，这两个密钥中只有一个投入使用。 另外一个密钥保留，让你能够定期轮换密钥。 要轮换密钥，可让辅助密钥成为活动密钥，并删除主密钥。 然后，可以创建一个新密钥，在下一次轮换时使用。 （出于安全原因，很多数据中心要求密钥轮换。）

我们建议遵循密钥轮换的以下最佳实践：

* 应定期轮换存储帐户密钥，以确保存储帐户不会被未经授权的用户访问。
* Azure 管理员应该定期使用 Azure 门户的“存储”部分来直接访问存储帐户，从而更改或重新生成主密钥或辅助密钥。

## <a name="protect-data-via-encryption"></a>通过加密保护数据

StorSimple 使用以下加密算法来保护存储在 StorSimple 解决方案组件中或在这些组件之间往返穿行的数据。

| 算法 | 密钥长度 | 协议/应用程序/注释 |
| --- | --- | --- |
| RSA |2048 |Azure 门户使用 RSA PKCS 1 v1.5 来加密发送到设备的配置数据：例如，存储帐户凭据、StorSimple 设备配置和云存储加密密钥。 |
| AES |256 |在将服务数据加密密钥从 StorSimple 设备发送到 Azure 门户之前，使用包含 CBC 的 AES 来加密它的公共部分。 在数据发送到云存储帐户之前，StorSimple 设备还使用该密钥来加密数据。 |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple 云设备安全性

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

以下是有关安全及 Microsoft Azure StorSimple 的部分问答。

**问：**我的服务受到安全威胁。 下一步我应该怎么做？

**答：**你应该立即更改服务数据加密密钥以及用于数据分层的存储帐户的存储帐户密钥。 有关说明，请转到：

* [更改服务数据加密密钥](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [存储帐户的密钥轮换](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**问：**我有一台新的 StorSimple 设备，需要服务注册密钥。 我该如何获取它？

**答：**首次创建 StorSimple 设备管理器服务时会创建该密钥。 使用 StorSimple 设备管理器服务连接到设备时，可以使用服务快速启动页面查看或者重新生成服务注册密钥。 生成新的服务注册密钥不会影响现有的已注册设备。 有关说明，请转到：

* [查看或重新生成服务注册密钥](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**问：**我丢失了服务数据加密密钥。 我该怎么办？

**答：**请与 Microsoft 支持部门联系。 支持部门可以登录到设备上的支持会话并帮助你获取密钥（前提是至少一台设备为联机状态）。 获取服务数据加密密钥后，应立即更改该密钥以保证仅自己知道新的密钥。 有关说明，请转到：

* [更改服务数据加密密钥](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**问：**我授权设备进行服务数据加密密钥更改，但却无法开始密钥更改过程。 我该怎么办？

**答：**如果超时期限已过，需要重新授权设备进行服务数据加密密钥更改，并重新开始该过程。

**问：**我更改了服务数据加密密钥，但无法在 4 小时内更新其他设备。 是否必须重新开始？

**答：**4 小时时限仅适用于启动更改操作。 一旦在授权的 StorSimple 设备上开始更新过程，授权会保持有效，直至所有设备都已更新。

**问：**我们的 StorSimple 管理员离开了公司。 我该怎么办？

**答：**请更改和重置用于访问 StorSimple 设备的密码，并更改服务数据加密密钥以确保新信息不会泄露给未获授权的人员。 有关说明，请转到：

* [使用 StorSimple 设备管理器服务更改 StorSimple 密码](storsimple-8000-change-passwords.md)
* [更改服务数据加密密钥](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [为 StorSimple 设备配置 CHAP](storsimple-8000-configure-chap.md)

**问：**我想要将 StorSimple Snapshot Manager 密码提供给连接到 StorSimple 设备的主机，但却想不起该密码。 我该怎么办？

**答：**如果忘记了密码，应该创建一个新密码。 然后务必通知所有现有用户密码已更改，要求他们更新客户端以使用新密码。 有关说明，请转到：

* [更改 StorSimple Snapshot Manager 密码](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [对设备进行身份验证](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**问：**用于远程访问 Windows PowerShell for StorSimple 的证书已在设备上更改。 如何更新我的远程访问客户端？

**答：**可以从 StorSimple 设备管理器服务下载新证书，然后即可将其安装到远程访问客户端的证书存储中。 有关说明，请转到：

* [Import-Certificate cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**问：**如果 StorSimple 设备管理器服务受到安全威胁，我的数据是否得到保护？

**答：**在 Web 浏览器中查看服务配置数据时，始终使用公钥加密该数据。 因为服务不具备访问公钥的权限，服务将无法看到任何数据。 如果 StorSimple 设备管理器服务受到安全威胁，则无任何影响，因为 StorSimple 设备管理器服务中未存储任何密钥。

**问：**如果有人获取了对数据加密证书的访问权限，我的数据是否会受到安全威胁？

**答：**Microsoft Azure 以加密格式存储客户的数据加密密钥（.pfx 文件）。 由于 .pfx 文件经过加密，而且 StorSimple 服务没有用于解密 .pfx 文件的服务数据加密密钥，因此单纯访问 .pfx 文件并不会造成机密泄露。

**问：**如果某个政府实体要求 Microsoft 提供我的数据，会发生什么情况？

**答：**由于所有数据是在服务上加密的，而私钥是保留在设备上的，该政府实体必须获得客户的允许才能获得数据。

## <a name="next-steps"></a>后续步骤

[部署 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。

