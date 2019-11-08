---
title: 通过 Azure Site Recovery 部署配置服务器，以便进行 VMware 灾难恢复 | Microsoft Docs
description: 本文介绍如何通过 Azure Site Recovery 部署配置服务器，以便进行 VMware 灾难恢复
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/15/2019
ms.author: ramamill
ms.openlocfilehash: 92b51b3955833bac6f87457a19e4d6359600a25a
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747863"
---
# <a name="deploy-a-configuration-server"></a>部署配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要部署本地配置服务器。 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。 将 VMware VM 复制到 Azure 时，本文可引导你完成部署配置服务器所需的步骤。 如果需要为物理服务器复制设置配置服务器，请参阅[设置配置服务器以便将物理服务器灾难恢复到 Azure](physical-azure-set-up-source.md)。

> [!TIP]
> 若要了解配置服务器在 Azure Site Recovery 体系结构中的作用，请参阅[VMware 到 Azure 灾难恢复体系结构](vmware-azure-architecture.md)。

## <a name="deploy-a-configuration-server-through-an-ova-template"></a>通过 .OVA 模板部署配置服务器

配置服务器必须设置为高度可用的 VMware VM，并且具有特定的最低硬件和大小要求。 为方便便捷地进行部署，Site Recovery 提供了一个可下载的开放虚拟化应用程序（.OVA）模板，用于设置符合此处列出的所有托管要求的配置服务器。

## <a name="prerequisites"></a>先决条件

以下各节汇总了配置服务器的最低硬件要求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 权限要求

您必须具有在 Azure Active Directory （Azure AD）中具有以下权限集之一的用户，才能向 Azure Site Recovery 服务注册配置服务器。

1. 用户必须具有应用程序开发人员角色才能创建应用程序。
    - 若要验证，请登录到 Azure 门户。</br>
    - 请参阅**Azure Active Directory** > **角色和管理员**。</br>
    - 验证是否已向用户分配了应用程序开发人员角色。 否则，请使用具有此权限的用户，或与[管理员联系以启用该权限](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)。
    
2. 如果无法分配应用程序开发人员角色，请确保将 "**用户可以注册应用程序**" 标志设置为 " **true** "，以便用户创建标识。 若要启用这些权限：
    - 登录到 Azure 门户。
    - 请参阅**Azure Active Directory** > **用户设置**。
    - 在 "**应用注册**" 下，"**用户可以注册应用程序**"，选择 **"是"** 。

      ![Azure AD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> *不支持*Active Directory 联合身份验证服务。 使用通过[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)管理的帐户。

## <a name="download-the-template"></a>下载模板

1. 在保管库中，转到“准备基础结构” **“源”**  > 。
2. 在“准备源”中，选择“+配置服务器”。
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中。
4. 下载配置服务器的 .OVA 模板。

   > [!TIP]
   >你还可以直接从[Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。

> [!NOTE]
> .OVA 模板提供的许可证是有效期为180天的评估许可证。 在此期限过后，必须购买许可证。

## <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在 "**文件**" 菜单上，选择 "**部署 OVF 模板**" 以启动 "**部署 OVF 模板**" 向导。

     ![部署 OVF 模板](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”。 使用精简设置选项可能会影响配置服务器的性能。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中：

    * 若要使用默认设置来设置 VM，请选择“部署后打开” **“完成”**  > 。
    * 若要添加其他网络接口，请清除“部署后打开”，并选择“完成”。 默认情况下，配置服务器模板是使用单个 NIC 部署的。 可以在部署后添加其他 NIC。

> [!IMPORTANT]
> 请不要在部署后更改资源配置（如内存、内核和 CPU 限制），也不要更改或删除配置服务器上已安装的服务或文件。 这些类型的更改会影响将配置服务器注册到 Azure 服务和配置服务器的性能。

## <a name="add-an-additional-adapter"></a>添加其他适配器

> [!NOTE]
> 如果计划在故障转移时保留源计算机的 IP 地址，并想要稍后故障回复到本地，则需要两个 Nic。 一个 NIC 连接到源计算机，另一个 NIC 用于 Azure 连接。

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加它。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”。
2. 在“硬件”中，选择“添加” **“以太网适配器”**  > 。 然后，选择“下一步”。
3. 选择适配器类型和网络。
4. 若要在打开 VM 时连接虚拟 NIC，请选择“上电时连接”。 然后，选择“下一步” **“完成”** “确定”。 >  > 

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>将配置服务器注册到 Azure Site Recovery 服务

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会在几秒钟内启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。</br>
    a. 使用的凭据必须有权访问配置服务器所要注册到的保管库。</br>
    b. 确保所选用户帐户有权在 Azure 中创建应用程序。 若要启用所需的权限，请遵循[Azure Active Directory 权限要求](#azure-active-directory-permission-requirements)一节中的指导原则。
7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 配置服务器管理向导会在几秒钟后自动启动。

### <a name="configure-settings"></a>配置设置

1. 在配置服务器管理向导中，选择“设置连接”。 在下拉框中，首先选择生成的进程服务器用于在源计算机上发现和推送移动服务的安装的 NIC。 然后选择配置服务器用于与 Azure 的连接的 NIC。 选择“保存”。 配置后无法更改此设置。 不要更改配置服务器的 IP 地址。 确保分配给配置服务器的 IP 是静态 IP 而不是 DHCP IP。
2. 在 "**选择恢复服务保管库**" 上，使用向[Azure Site Recovery 服务注册配置服务器](#register-the-configuration-server-with-azure-site-recovery-services)的步骤6中使用的凭据登录到 Microsoft Azure。
3. 登录后，选择你的 Azure 订阅以及相关的资源组和保管库。

    > [!NOTE]
    > 注册后，不能灵活地更改恢复服务保管库。
    > 更改恢复服务保管库需要解除配置服务器与当前保管库的配置，并停止配置服务器下所有受保护的虚拟机的复制。 若要了解详细信息，请参阅[为 VMWARE VM 灾难恢复管理配置服务器](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

4. **安装第三方软件**时：

    |方案   |执行的步骤  |
    |---------|---------|
    |能否手动下载和安装 MySQL？     |  是的。 下载 MySQL 应用程序，将其放在**C:\Temp\ASRSetup**文件夹中，然后手动安装。 接受条款并选择 "**下载并安装**" 后，门户会显示 "*已安装*"。 可以继续下一步。       |
    |可以避免联机下载 MySQL 吗？     |   是的。 将 MySQL 安装程序应用程序置于 C:\Temp\ASRSetup 文件夹中。 接受条款，选择 "**下载并安装**"，门户将使用所添加的安装程序安装应用程序。 安装完成后，请继续执行下一步。    |
    |我想通过 Azure Site Recovery 下载并安装 MySQL。    |  接受许可协议，然后选择 "**下载并安装**"。 安装完成后，请继续执行下一步。       |

5. 在 "**验证设备配置**" 中，系统会先验证先决条件，然后再继续。
6. 在 "**配置 vCenter Server/VSphere ESXi 服务器**" 中，输入要复制的 vm 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
7. 输入配置服务器用来连接到 VMware 服务器的凭据。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加” **“继续”**  > 。 此处输入的凭据会在本地保存。
8. 在 "**配置虚拟机凭据**" 中，输入虚拟机的用户名和密码，以便在复制过程中自动安装移动服务。 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。 对于 Linux，请提供根帐户的详细信息。
9. 选择“完成配置”以完成注册。
10. 注册完成后，打开 "Azure 门户"，验证配置服务器和 VMware 服务器是否已在**恢复服务保管库**上列出 > **管理** > **Site Recovery 基础结构** > **配置服务器**。

## <a name="upgrade-the-configuration-server"></a>升级配置服务器

若要将配置服务器升级到最新版本，请参阅为[VMWARE VM 灾难恢复管理配置服务器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有关如何升级所有 Site Recovery 组件的说明，请参阅[Site Recovery 中的服务更新](service-updates-how-to.md)。

## <a name="manage-the-configuration-server"></a>管理配置服务器

若要避免正在进行的复制中断，请确保在将配置服务器注册到保管库后配置服务器的 IP 地址不会更改。 若要了解有关常见配置服务器管理任务的详细信息，请参阅[为 VMWARE VM 灾难恢复管理配置服务器](vmware-azure-manage-configuration-server.md)。

## <a name="faqs"></a>常见问题

* 通过 OVF 部署的配置服务器上提供的许可证有多长？ 如果未重新激活许可证，会发生什么情况？

    .OVA 模板提供的许可证是有效期为180天的评估版许可证。 在过期之前，需要激活许可证。 否则，可能会导致频繁关闭配置服务器并导致障碍复制活动。 有关详细信息，请参阅为[VMWARE VM 灾难恢复管理配置服务器](vmware-azure-manage-configuration-server.md#update-windows-license)。

* 是否可以使用安装了配置服务器以用于不同用途的 VM？

    不能。 将 VM 用于配置服务器的唯一目的。 确保遵循[先决条件](#prerequisites)中提到的所有规范，以便高效管理灾难恢复。
* 可以将已在配置服务器中注册的保管库切换为新创建的保管库吗？

    不能。 在将保管库注册到配置服务器后，不能对其进行更改。
* 能否使用同一配置服务器来保护物理计算机和虚拟机？

    是的。 同一配置服务器可用于复制物理机和虚拟机。 但是，物理计算机只能故障回复到 VMware VM。
* 配置服务器的用途是什么？

    若要了解有关配置服务器及其功能的详细信息，请参阅[VMware 到 Azure 复制体系结构](vmware-azure-architecture.md)。
* 在哪里可以找到最新版本的配置服务器？

    有关通过门户升级配置服务器的步骤，请参阅[升级配置服务器](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。 有关如何升级所有 Site Recovery 组件的说明，请参阅[Site Recovery 中的服务更新](https://aka.ms/asr_how_to_upgrade)。
* 在哪里可以下载配置服务器的密码？

    若要下载通行短语，请参阅[管理用于 VMWARE VM 灾难恢复的配置服务器](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。
* 是否可以更改通行短语？

    不能。 请勿更改配置服务器的密码。 密码更改会断开受保护计算机的复制，并导致严重的运行状况状态。
* 在哪里可以下载保管库注册密钥？

    在**恢复服务保管库**中，选择 "**管理** > **Site Recovery 基础结构** > **配置服务器**"。 在“服务器”中，选择“下载注册密钥”以下载保管库凭据文件。
* 是否可以克隆现有配置服务器，并将其用于复制业务流程？

    不能。 不支持使用克隆的配置服务器组件。 横向扩展进程服务器的克隆也是不受支持的方案。 克隆 Site Recovery 组件会影响正在进行的复制。

* 能否更改配置服务器的 IP？

    不能。 不要更改配置服务器的 IP 地址。 确保分配给配置服务器的所有 Ip 均为静态 Ip 而不是 DHCP Ip。
* 能否在 Azure 上设置配置服务器？

    在本地环境中设置配置服务器，使其能够与 v 中心直接连线，并最大程度地减少数据传输延迟。 可出于[故障回复目的](vmware-azure-manage-configuration-server.md#failback-requirements)对配置服务器进行计划备份。

有关配置服务器的详细信息，请参阅[配置服务器](vmware-azure-common-questions.md#configuration-server)常见问题解答。

## <a name="troubleshoot-deployment-issues"></a>排查部署问题

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>后续步骤

设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。
