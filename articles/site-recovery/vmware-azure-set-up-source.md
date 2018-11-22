---
title: 使用 Azure Site Recovery 为到 Azure 的 VMware VM 灾难恢复设置源环境和配置服务器 | Microsoft Docs
description: 本文介绍了如何使用 Azure Site Recovery 为到 Azure 的 VMware VM 灾难恢复设置本地环境和配置服务器。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566306"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>设置源环境和配置服务器

本文介绍了如何使用 [Azure Site Recovery](site-recovery-overview.md) 为到 Azure 的 VMware VM 灾难恢复设置源本地环境。 它包括将本地计算机设置为 Site Recovery 配置服务器和自动发现本地 VM 的步骤。 

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成以下操作

- 使用 [Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)规划了灾难恢复部署。 规划器有助于确保具有足够的资源和带宽用于组织中的灾难恢复和恢复点目标 (RPO)。
- 在 [Azure 门户](http://portal.azure.com)中[设置 Azure 资源](tutorial-prepare-azure.md)。
- [设置本地 VMware](vmware-azure-tutorial-prepare-on-premises.md) 资源，包括用于自动发现本地 VMware VM 的专用帐户。
- 可以查看有关配置服务器部署和管理的[常见问题](vmware-azure-common-questions.md#configuration-server)。


## <a name="choose-protection-goals"></a>选择保护目标

1. 在“恢复服务保管库”中，选择保管库名称。 
2. 在“入门”中，选择“Site Recovery”， 然后选择“准备基础结构”。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”。 然后选择“确定”。

## <a name="about-the-configuration-server"></a>关于配置服务器

将 VMware VM 的灾难恢复设置为 Azure 时，可以部署本地配置服务器。

- 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。
- 将配置服务器部署为 VMware VM。
- Site Recovery 提供 OVA 模板，可从 Azure 门户中下载并导入 vCenter Server 以设置配置服务器 VM。
- 了解有关配置服务器组件和进程的[详细信息](vmware-azure-architecture.md)。


>[!NOTE]
如果要为本地物理计算机到 Azure 的灾难恢复部署配置服务器，请不要使用这些说明。 对于此情况，请按照[本文](physical-azure-set-up-source.md)的说明进行操作。


## <a name="before-you-deploy-the-configuration-server"></a>在部署配置服务器之前

如果使用 OVA 模板将配置服务器安装为 VMware VM，则将安装具有所有先决条件的 VM。 如果要查看先决条件，请使用以下文章。

- [了解](vmware-azure-configuration-server-requirements.md)配置服务器的硬件、软件和容量要求。
- 如果要复制多个 VMware VM，则应查看[容量规划注意事项](site-recovery-plan-capacity-vmware.md)，然后运行用于 VMWare 复制的 [Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)工具。
- OVA 模板的 Windows 许可证是有效期为 180 天的评估许可证。 在此之后，需要使用有效许可证激活 Windows。 
- OVA 模板提供了将配置服务器设置为 VMware VM 的简单方法。 如果出于某种原因需要在不使用模板的情况下设置 VMware VM，请查看先决条件，并遵照[本文](physical-azure-set-up-source.md)的手册说明进行操作。
- Azure 帐户需要创建 Azure AD 应用的权限。


>[!IMPORTANT]
如本文所述，必须部署配置服务器。 不支持复制或克隆现有配置服务器。

### <a name="set-up-azure-account-permissions"></a>设置 Azure 帐户权限

租户/全局管理员可为该帐户分配创建 Azure AD 应用的权限，或者可将应用程序开发人员角色（具有权限）分配给该帐户。


租户/全局管理员可以为帐户授予权限，如下所示：

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”。
2. 管理员应将“应用注册”设置为“是”。

 > [!NOTE]
 > 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

或者，租户/全局管理员拥有将角色分配给帐户的权限。 [了解详细信息](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

 


## <a name="download-the-ova-template"></a>下载 OVA 模板

1. 在保管库中，转到“准备基础结构” > “源”。
2. 在“准备源”中，选择“+配置服务器”。
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中。
4. 下载配置服务器的开放虚拟应用程序 (OVA) 模板。

  > [!TIP]
>还可以从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。


## <a name="import-the-ova-template-in-vmware"></a>在 VMware 中导入 OVA 模板

1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导。

     ![OVF 模板](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中，若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”。
9. 默认情况下，使用单个 NIC 部署 VM。 若要添加其他 NIC，请清除“部署后打开”，然后单击“完成”。 然后进行下一步骤操作。

## <a name="add-an-adapter-to-the-configuration-server"></a>向配置服务器添加适配器

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加它。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”。
2. 在“硬件”中，选择“添加” > “以太网适配器”。 然后，选择“下一步”。
3. 选择适配器类型和网络。
4. 若要在打开 VM 时连接虚拟 NIC，请选择“上电时连接”。 然后，选择“下一步” > “完成” > “确定”。

## <a name="register-the-configuration-server"></a>注册配置服务器 
在 Site Recovery 保管库中，打开 VM 并注册配置服务器。

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。



## <a name="set-up-the-configuration-server"></a>设置配置服务器

作为部署的一部分，需要在配置服务器 VM 上安装 MySQL。 可以通过多种方式实现此目的：

- 下载 Site Recovery 并安装 MySQL。 若要使用此选项，无需在开始设置配置服务器之前执行任何操作。
- 手动下载并安装 MySQL：设置配置服务器之前，请下载 MySQL 安装程序并将其放入 C:\Temp\ASRSetup 文件夹。 现在安装 MySQL。 
- 手动下载并安装 Site Recovery。 设置配置服务器之前，请下载 MySQL 安装程序并将其放入 C:\Temp\ASRSetup 文件夹。


1. 首次登录到 VM 时，会启动 Azure Site Recovery 配置工具。
2. 指定用于在 Site Recovery 保管库中注册配置服务器的名称。 然后，选择“下一步”。
3. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。 请注意，帐户必须有权访问要在其中注册配置服务器的保管库。
4. 该工具将执行一些配置任务，然后重新启动。
5. 再次登录到计算机。 配置服务器管理向导会在数秒内自动启动。
6. 在向导中，选择“设置连接”。
7. 选择进程服务器（在配置服务器上默认运行）用于接收来自 VM 的复制流量的 NIC。
8. 再选择“保存”。 请注意，在注册配置服务器后，无法更改保管库设置。 
9. 在“选择恢复服务保管库”中，登录到 Microsoft Azure，选择自己的 Azure 订阅以及相关的资源组和保管库。 
10. 在“安装第三方软件”中，安装 MySQL：
     - 如果 Site Recovery 正在处理 MySQL 下载和安装，请单击“下载并安装”。 等待安装完成，然后继续执行向导。
     - 如果下载了 MySQL，且 Site Recovery 会安装它，请单击“下载并安装”。 等待安装完成，然后继续执行向导。
     - 如果手动下载并安装了 MySQL，请单击“下载并安装”。 应用将显示为“已安装”。 请继续执行向导。
11. 在“验证设备配置”中，安装程序会在继续操作之前验证先决条件。 
12. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter 服务器或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口，并为保管库中的 VMware 服务器指定友好名称。
13. 输入配置服务器将用于连接到 VMware 服务器的凭据，并自动发现可用于复制的 VMware VM。 选择“添加” >  “继续”。 这些凭据将保存在本地。
14. 在“配置虚拟机凭据”中，指定为 VM 启用复制时 Site Recovery 将用于自动安装移动服务的凭据。
    - 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。
    - 对于 Linux，请提供根帐户的详细信息。
15. 选择“完成配置”以完成注册。
16. 完成注册后，打开 Azure 门户，检查配置服务器和 VMware 服务器是否出现在“恢复服务保管库” > “管理” > “Site Recovery 基础结构” > “配置服务器”中。


## <a name="exclude-antivirus-on-the-configuration-server"></a>排除配置服务器上的防病毒软件

如果配置服务器 VM 上正在运行防病毒软件，请确保从防病毒操作中排除以下文件夹。 这可确保复制和连接按预期方式工作： 

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Site Recovery 安装目录。 例如：E:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>后续步骤
- 如果遇到问题，请查看配置服务器的[常见问题](vmware-azure-common-questions.md#configuration server)和[疑难解答](vmware-azure-troubleshoot-configuration-server.md)。
- 现在，在 Azure 中[设置目标环境](./vmware-azure-set-up-target.md)。
