---
title: 通过 Azure Site Recovery 部署配置服务器，以便进行 VMware 灾难恢复 | Microsoft Docs
description: 本文介绍如何通过 Azure Site Recovery 部署配置服务器，以便进行 VMware 灾难恢复
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 4d944bec9d1cf73263d9deb54d0d9b3f6fc5532f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285027"
---
# <a name="deploy-a-configuration-server"></a>部署配置服务器

使用 [Azure Site Recovery](site-recovery-overview.md) 进行 VMware VM 和物理服务器到 Azure 的灾难恢复时，需要部署本地配置服务器。 配置服务器协调本地 VMware 与 Azure 之间的通信。 它还管理数据复制。 将 VMware VM 复制到 Azure 时，本文可引导你完成部署配置服务器所需的步骤。 如果需要为物理服务器复制设置配置服务器，[请按此文中的说明进行操作](physical-azure-set-up-source.md)。

## <a name="prerequisites"></a>先决条件

建议将配置服务器部署为高可用性 VMware VM。 下表汇总了配置服务器要求。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



### <a name="prepare-for-mysql-installation"></a>准备安装 MySQL

MySQL 必须安装在配置服务器上。 可使用以下方法之一来执行此操作：

- 当配置服务器管理向导运行时，让 Site Recovery 下载并安装它。 你不必执行任何特定操作。
- 手动下载 MySQL，并将它置于文件夹 C:\Temp\ASRSetup 中。 然后运行安装程序。 当向导运行时，Site Recovery 将识别它已安装。
- 手动下载 MySQL，并将它置于文件夹 C:\Temp\ASRSetup 中。 当向导运行时，它会找到安装文件，并从此位置安装它。 


## <a name="capacity-planning"></a>容量计划

配置服务器的大小要求取决于潜在的数据更改率。 使用此表作为指南。

| **CPU** | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- | --- |
| 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz） |16 GB |300 GB |500 GB 或更少 |复制少于 100 台计算机。 |
| 12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz） |18 GB |600 GB |500 GB 到 1 TB |复制 100-150 台计算机。 |
| 16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz） |32 GB |1 TB |1 TB 到 2 TB |复制 150-200 台计算机。 |


如果要复制 VMware VM，请阅读有关[容量规划注意事项](/site-recovery-plan-capacity-vmware.md)的更多内容。 为 VMWare 复制运行 [Deployment Planner 工具](site-recovery-deployment-planner.md)。



## <a name="download-the-template"></a>下载模板

Site Recovery 提供可下载的模板，以将配置服务器设置为高度可用的 VMware VM。 

1. 在保管库中，转到“准备基础结构” > “源”。
2. 在“准备源”中，选择“+配置服务器”。
3. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中。
4. 下载配置服务器的开放虚拟化格式 (OVF) 模板。

  > [!TIP]
  可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。


## <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板


1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导。

     ![OVF 模板](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”。
4. 在余下的向导页中，接受默认设置。
5. 在“准备完成”中：

    * 若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”。

    * 若要添加其他网络接口，请清除“部署后打开”，并选择“完成”。 默认情况下，配置服务器模板是使用单个 NIC 部署的。 可以在部署后添加其他 NIC。


## <a name="add-an-additional-adapter"></a>添加其他适配器

若要将其他 NIC 添加到配置服务器，请在将服务器注册到保管库中之前添加它。 注册后不支持添加其他适配器。

1. 在 vSphere 客户端库存中，右键单击 VM 并选择“编辑设置”。
2. 在“硬件”中，选择“添加” > “以太网适配器”。 然后，选择“下一步”。
3. 选择适配器类型和网络。 
4. 若要在打开 VM 时连接虚拟 NIC，请选择“打开时连接”。 然后，选择“下一步” > “完成” > “确定”。
 

## <a name="register-the-configuration-server"></a>注册配置服务器 

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器所要注册到的保管库。
7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 配置服务器管理向导会自动启动。

### <a name="configure-settings"></a>配置设置

1. 在配置服务器管理向导中，选择“设置连接”。 选择要接收复制流量的 NIC，然后选择“保存”。 配置后无法更改此设置。
2. 在“选择恢复服务保管库”中，选择自己的 Azure 订阅以及相关的资源组和保管库。
3. 在“安装第三方软件”中，接受许可协议。 按照[用于安装 MySQL 的方法](#prepare-for-mysql-installation)安装 MySQL。
4. 选择“安装 VMware PowerLCI”。 执行此操作之前，请确保所有浏览器窗口已关闭。 然后选择“继续”。
5. 在“验证设备配置”中验证先决条件，然后继续。
6. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口，以及保管库中的 VMware 服务器的友好名称。
7. 输入配置服务器用来连接到 VMware 服务器的凭据。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加”，然后选择“继续”。
8. 在“配置虚拟机凭据”中，输入用于在计算机上自动安装 Azure Site Recovery 移动服务的用户名和密码（如果已启用复制）。 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。 对于 Linux，请提供根帐户的详细信息。
9. 选择“完成配置”以完成注册。 
10. 注册完成后，请在 Azure 门户中确认配置服务器和 VMware 服务器是否已在保管库中的“源”页上列出。 然后，选择“确定”以配置目标设置。


## <a name="upgrade-the-configuration-server"></a>升级配置服务器

要将配置服务器升级到最新版本，请阅读[此处](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)提供的步骤


## <a name="troubleshoot-deployment-issues"></a>排查部署问题

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>后续步骤

设置 [VMware VM](vmware-azure-tutorial.md) 到 Azure 的灾难恢复。
