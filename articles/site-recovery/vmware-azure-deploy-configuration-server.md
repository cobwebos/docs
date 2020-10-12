---
title: 在 Azure Site Recovery 中部署配置服务器
description: 本文介绍如何通过 Azure Site Recovery 部署配置服务器，以便进行 VMware 灾难恢复
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: f6c47d4cbfe6311333d95b07c0553afa2b3bb15c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287729"
---
# <a name="deploy-a-configuration-server"></a>部署配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要部署本地配置服务器。 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。 将 VMware VM 复制到 Azure 时，本文可引导你完成部署配置服务器所需的步骤。 如果需要为物理服务器复制设置配置服务器，请参阅[为物理服务器到 Azure 的灾难恢复设置配置服务器](physical-azure-set-up-source.md)。

> [!TIP]
> 若要了解配置服务器的角色（Azure Site Recovery 体系结构的一部分），请参阅 [VMware 到 Azure 的灾难恢复的体系结构](vmware-azure-architecture.md)。

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>通过 OVA 模板部署配置服务器

必须将配置服务器设置为一个高度可用的 VMware VM，它符合一定的最低硬件和大小调整要求。 为了便于部署，Site Recovery 提供了一个可下载的开放虚拟化应用程序 (OVA) 模板，用于设置符合此处列出的所有强制性要求的配置服务器。

## <a name="prerequisites"></a>先决条件

以下部分汇总了配置服务器的最低硬件要求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 权限要求

只有拥有 Azure Active Directory (Azure AD) 中设置的以下权限之一的用户才能将配置服务器注册到 Azure Site Recovery 服务。

1. 创建应用程序的用户必须拥有应用程序开发人员角色。
    - 若要验证角色，请登录到 Azure 门户。</br>
    - 转到“Azure Active Directory” > “角色和管理员”。</br>
    - 检查应用程序开发人员角色是否已分配到该用户。 如果没有，请使用拥有此权限的用户，或[联系管理员启用该权限](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md#assign-roles)。
    
2. 如果无法分配应用程序开发人员角色，请确保“用户可以注册应用程序”标志设置为 **true**，使该用户能够创建标识。 若要启用这些权限：
    - 登录到 Azure 门户。
    - 转到“Azure Active Directory” > “用户设置”。
    - 在“应用注册”下，对于“用户可以注册应用程序”，请选择“是”。   

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> 不支持 Active Directory 联合身份验证服务。  请使用通过 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 管理的帐户。

## <a name="download-the-template"></a>下载模板

1. 在保管库中，转到“准备基础结构” > “源”。
2. 在“准备源”中，选择“+配置服务器”。  
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中    。
4. 下载配置服务器的 OVA 模板。

   > [!TIP]
   >你还可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。

> [!NOTE]
> 通过 OVA 模板提供的许可证是有效期为 180 天的评估许可证。 此期限过后，必须购买许可证。

## <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

1. 使用 VMware vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导    。

     ![部署 OVF 模板](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置  。
4. 在“查看详细信息”中，选择“下一步”。  
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置   。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”    。 使用精简预配选项可能会影响配置服务器的性能。
7. 在余下的向导页中，接受默认设置。
8.  在“准备完成”中：

    * 若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”。
    * 若要添加其他网络接口，请清除“部署后打开”，并选择“完成”   。 默认情况下，配置服务器模板是使用单个 NIC 部署的。 可以在部署后添加其他 NIC。

> [!IMPORTANT]
> 不要在部署后更改资源配置（例如内存、核心数和 CPU 限制），也不要修改或删除配置服务器上已安装的服务或文件。 此类更改会影响配置服务器在 Azure 服务中的注册以及配置服务器的性能。

## <a name="add-an-additional-adapter"></a>添加其他适配器

> [!NOTE]
> 如果你打算在故障转移时保留源计算机的 IP 地址，并希望以后再故障回复到本地，需要提供两个 NIC。 一个 NIC 连接到源计算机，另一个 NIC 用于 Azure 连接。

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加它。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”  。
2. 在“硬件”中，选择“添加” > “以太网适配器”。 然后，选择“下一步”  。
3. 选择适配器类型和网络。
4. 若要在打开 VM 时连接虚拟 NIC，请选择“上电时连接”  。 然后，选择“下一步” > “完成” > “确定”。

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>将配置服务器注册到 Azure Site Recovery 服务

1. 通过 VMware vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会在数秒内启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”  。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅  。</br>
    a. 使用的凭据必须有权访问配置服务器所要注册到的保管库。</br>
    b. 确保所选用户帐户有权在 Azure 中创建应用程序。 若要启用所需的权限，请遵循 [Azure Active Directory 权限要求](#azure-active-directory-permission-requirements)部分中的指导原则。
7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 在数秒内，配置服务器管理向导会自动启动。

### <a name="configure-settings"></a>配置设置

1. 在配置服务器管理向导中，选择“设置连接”。  在下拉框中，首先选择内置进程服务器用于发现的 NIC，并将移动服务的安装推送到源计算机上。 然后选择配置服务器用来与 Azure 建立连接的 NIC。 选择“保存”  。 配置后无法更改此设置。 不要更改配置服务器的 IP 地址。 确保分配给配置服务器的 IP 是静态 IP，而不是 DHCP IP。
2. 在 " **选择恢复服务保管库**" 上，使用向 [Azure Site Recovery 服务注册配置服务器](#register-the-configuration-server-with-azure-site-recovery-services)的步骤6中使用的凭据登录到 Microsoft Azure。
3. 登录后，选择你的 Azure 订阅以及相关的资源组和保管库。

    > [!NOTE]
    > 注册后，不能随意更改恢复服务保管库。
    > 更改恢复服务保管库需要从当前保管库取消关联配置服务器，配置服务器下所有受保护虚拟机的复制将会停止。 有关详细信息，请参阅[管理用于 VMware VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

4. 在“安装第三方软件”中： 

    |方案   |执行的步骤  |
    |---------|---------|
    |是否可以下载并手动安装 MySQL？     |  是的。 下载 MySQL 应用程序，将其置于 **C:\Temp\ASRSetup** 文件夹中，然后手动安装。 接受条款且选择“下载并安装”后，门户将显示“已安装”。   可以继续下一步。       |
    |可以避免联机下载 MySQL 吗？     |   是的。 将 MySQL 安装程序应用程序置于 C:\Temp\ASRSetup  文件夹中。 接受条款，选择“下载并安装”，然后，门户将使用添加的安装程序来安装该应用程序。  安装完成后，转到下一步。    |
    |我想要通过 Azure Site Recovery 下载并安装 MySQL。    |  接受许可协议，选择“下载并安装”  。 安装完成后，转到下一步。       |

5. 在“验证设备配置”中验证先决条件，然后继续  。
6. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址  。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
7. 输入配置服务器用来连接到 VMware 服务器的凭据。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加” > “继续”。 此处输入的凭据会在本地保存。
8. 在“配置虚拟机凭据”  中，输入虚拟机的用户名和密码，以便在复制过程中自动安装移动服务。 对于 Windows  计算机，该帐户在要复制的计算机上需有本地管理员特权。 对于 Linux  ，请提供根帐户的详细信息。
9. 选择“完成配置”以完成注册  。
10. 完成注册后，打开 Azure 门户，验证配置服务器和 VMware 服务器是否在“恢复服务保管库” > “管理” > “Site Recovery 基础结构” > “配置服务器”中列出。

## <a name="upgrade-the-configuration-server"></a>升级配置服务器

若要将配置服务器升级到最新版本，请参阅[管理用于 VMware VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有关如何升级所有 Site Recovery 组件的说明，请参阅 [Site Recovery 中的服务更新](service-updates-how-to.md)。

## <a name="manage-the-configuration-server"></a>管理配置服务器

为避免正在进行的复制中断，请确保在配置服务器注册到保管库后配置服务器的 IP 地址不会更改。 若要详细了解常见的配置服务器管理任务，请参阅[管理用于 VMware VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md)。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

参阅我们的[故障排除文章](vmware-azure-troubleshoot-configuration-server.md)来解决部署和连接问题。

## <a name="faqs"></a>常见问题解答

* 通过 OVF 部署在配置服务器上的许可证有效期是多长？ 如果不重新激活许可证会发生什么情况？

    通过 OVA 模板提供的许可证是有效期为 180 天的评估许可证。 在过期之前，需要激活该许可证。 否则可能会导致配置服务器频繁关闭，并妨碍复制活动。 有关详细信息，请参阅[管理用于 VMware VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md#update-windows-license)。

* 是否可以使用安装有配置服务器的 VM 实现其他用途？

    否。 请仅将该 VM 单独用于配置服务器。 请确保遵循[先决条件](#prerequisites)中提到的所有规范，以便有效管理灾难恢复。
* 可以将已在配置服务器中注册的保管库切换为新创建的保管库吗？

    否。 将保管库注册到配置服务器后，它无法更改。
* 是否可以使用同一配置服务器来同时保护物理计算机和虚拟机？

    是的。 同一配置服务器可以用于复制物理计算机和虚拟机。 但是，物理计算机仅可故障回复到 VMware VM。
* 配置服务器有何用途，可用于何处？

    若要详细了解配置服务器及其功能，请参阅 [VMware 到 Azure 的复制的体系结构](vmware-azure-architecture.md)。
* 在哪里可以找到最新版本的配置服务器？

    有关通过门户升级配置服务器的步骤，请参阅[升级配置服务器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有关如何升级所有 Site Recovery 组件的说明，请参阅 [Site Recovery 中的服务更新](https://aka.ms/asr_how_to_upgrade)。
* 在哪里可以下载配置服务器的密码？

    若要下载通行短语，请参阅[管理用于 VMware VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。
* 是否可以更改通行短语？

    否。 请不要更改配置服务器的通行短语。 通行短语的更改会中断受保护计算机的复制，并会导致关键运行状况状态。
* 在哪里可以下载保管库注册密钥？

    在“恢复服务保管库”中，选择“管理” > “Site Recovery 基础结构” > “配置服务器”。 在“服务器”中，选择“下载注册密钥”以下载保管库凭据文件。  
* 是否可以克隆现有配置服务器并将其用于复制业务流程？

    否。 不支持使用克隆的配置服务器组件。 克隆横向扩展进程服务器也是不受支持的方案。 克隆 Site Recovery 组件会影响正在进行的复制。

* 是否可以更改配置服务器的 IP？

    否。 不要更改配置服务器的 IP 地址。 确保分配给配置服务器的所有 IP 是静态 IP，而不是 DHCP IP。
* 是否可以在 Azure 上设置配置服务器？

    使用与 v-Center 的直通连接在本地环境中设置配置服务器，这样可将数据传输延迟降至最低。 可出于[故障回复目的](vmware-azure-manage-configuration-server.md#failback-requirements)对配置服务器进行计划备份。

* 是否可以更改配置服务器或横向扩展进程服务器上的缓存驱动程序？

    不可以。设置完成后，无法更改缓存驱动程序。

有关配置服务器的更多常见问题解答，请参阅[配置服务器常见问题解答](vmware-azure-common-questions.md#configuration-server)。

## <a name="next-steps"></a>后续步骤

设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。
