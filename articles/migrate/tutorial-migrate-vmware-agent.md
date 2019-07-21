---
title: 使用基于代理的 Azure Migrate 服务器迁移工具将本地 VMware VM 迁移到 Azure | Microsoft Docs
description: 本文介绍如何使用基于代理的 Azure Migrate 服务器迁移将本地计算机迁移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261413"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>将 VMware VM 迁移到 Azure（使用基于代理的方法）

本文介绍如何使用基于代理的 Azure Migrate 服务器迁移工具将本地 VMware VM 迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与 AWS/GCP VM 实例的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。


本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 设置源环境，并为基于代理的迁移部署 Azure Migrate 复制设备。
> * 为迁移设置目标环境。
> * 设置复制策略。
> * 启用复制。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 VMware 评估和迁移的操作指南。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="before-you-begin"></a>开始之前

在将 VMware VM 迁移到 Azure 之前，我们建议你尝试使用 Azure Migrate 服务器评估工具对其进行评估。 按如下述设置评估：

1. 遵循相应的教程[准备 Azure 和 VMware](tutorial-prepare-vmware.md) 以进行评估。
2. 然后，遵循[此教程](tutorial-assess-vmware.md)设置用于评估的 Azure Migrate 设备，并发现和评估 VM。


尽管我们建议你尝试运行评估，但在迁移 VM 之前不一定非要这样做。

## <a name="migration-methods"></a>迁移方法

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 无代理复制。 迁移 VM 且无需在其上安装任何组件。
- 基于代理的迁移。 或复制。 在要复制的 VM 上安装代理（移动服务代理）。

若要确定是使用无代理迁移还是基于代理的迁移，请查看以下文章：

- [了解](server-migrate-overview.md) VMware 迁移选项。
- [查看](server-migrate-overview.md#agentless-migration-limitations)无代理迁移的限制。
- [遵循此文](tutorial-migrate-vmware.md)尝试进行无代理迁移。



## <a name="prerequisites"></a>先决条件

开始学习本教程之前，应做好以下准备：

1. [查看](migrate-architecture.md) VMware 迁移体系结构。
2. 确保为 Azure 帐户分配“虚拟机参与者”角色，以便你有权执行以下操作：

    - 在所选资源组中创建 VM。
    - 在所选虚拟网络中创建 VM。
    - 写入 Azure 托管磁盘。 

3. [设置 Azure 网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 本地计算机将复制到 Azure 托管磁盘。 在迁移期间故障转移到 Azure 时，将基于这些托管磁盘创建 Azure VM，并将其加入到在设置迁移时指定的 Azure 网络。


## <a name="prepare-azure"></a>准备 Azure

如果你已使用 Azure Migrate 服务器评估运行了评估，则可以跳过本部分中的说明，因为这些步骤已完成。 

如果你尚未运行评估，则需要设置 Azure 权限，然后才能使用 Azure Migrate 服务器迁移进行迁移。

- **创建项目**：你的 Azure 帐户需要有权创建 Azure Migrate 项目。 
- **注册 Azure Migrate 复制设备**：复制设备将在 Azure 帐户中创建并注册 Azure Active Directory 应用。 需要委托此操作的权限。
- **创建 Key Vault**：若要使用 Azure Migrate 服务器迁移工具迁移 VMware VM，Azure Migrate 需在资源组中创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 若要创建保管库，需要对 Azure Migrate 项目所在的资源组拥有角色分配权限。 


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-permissions-to-register-the-replication-appliance"></a>分配注册复制设备的权限

对于基于代理的迁移，请委托 Azure Migrate 服务器迁移的权限，以便能够在帐户中创建和注册 Azure AD 应用。 可使用以下方法之一分配权限：

- 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
- 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

值得注意的是：

- 除上述权限外，应用对订阅不拥有任何其他访问权限。
- 只有在注册新的复制设备时，你才需要这些权限。 设置复制设备后可以删除这些权限。 


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”    。
2. 管理员应将“应用注册”设置为“是”   。

    ![Azure AD 权限](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色 

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="assign-permissions-to-create-key-vault"></a>分配创建 Key Vault 的权限

按如下所述分配对 Azure Migrate 项目所在的资源组的角色分配权限：

1. 在 Azure 门户上的资源组中，选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。  需要“所有者”（或“参与者”和“用户访问管理员”）权限。   
3. 如果没有所需的权限，可请求资源组所有者分配这些权限。 


## <a name="prepare-on-premises-vmware"></a>准备本地 VMware

### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Azure Migrate 服务器迁移需要有权访问 VMware 服务器，以便：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 你需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

按如下所述创建此帐户：

1. 若要使用专用帐户，请 vCenter 级别创建一个角色。 为该角色指定一个名称，例如 Azure_Site_Recovery  。
2. 为该角色分配下表中汇总的权限。
3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

#### <a name="vmware-account-permissions"></a>VMware 帐户权限

**任务** | **角色/权限** | **详细信息**
--- | --- | ---
**VM 发现** | 至少一个只读用户<br/><br/> 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）   。
**完全复制、故障转移、故障回复** |  创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将该角色分配到 VMware 用户或组<br/><br/> 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）   。

### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的计算机上安装移动服务。

- 为计算机启用了复制时，Azure Migrate 复制设备可以执行此服务的推送安装，你也可以手动安装或使用安装工具安装。
- 在本教程中，我们将使用推送安装来安装移动服务。
- 若要执行推送安装，需要准备一个供 Azure Migrate 服务器迁移用来访问 VM 的帐户。

按如下所述准备此帐户：

1. 准备一个在 VM 上具有安装权限的域或本地帐户。
2. 对于 Windows VM，如果使用的不是域帐户，请在本地计算机上禁用远程用户访问控制：在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下，添加 DWORD 项 **LocalAccountTokenFilterPolicy** 并将值设为 。
3. 对于 Linux VM，请在源 Linux 服务器上准备一个 root 帐户。


### <a name="check-vmware-requirements"></a>检查 VMware 要求

确保 VMware 服务器和 VM 符合迁移到 Azure 的要求。 


> [!NOTE]
> 使用 Azure Migrate 服务器迁移进行基于代理的迁移依赖于 Azure Site Recovery 服务的功能。 某些要求可能提供了 Site Recovery 文档的链接。

1. [验证](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware 服务器要求。
2. [验证](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)要迁移的 VM 的支持要求。
3. 验证 VM 设置。 复制到 Azure 的本地 VM 必须符合 [Azure VM 要求](migrate-support-matrix-vmware.md#azure-vm-requirements)。



## <a name="add-the-azure-migrate-server-migration-tool"></a>添加 Azure Migration 服务器迁移工具

如果你未遵循相应的教程评估 VMware VM，请设置一个 Azure Migrate 项目，并添加 Azure Migrate 服务器迁移工具：

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。 
2. 在“服务”下选择“Azure Migrate”。  

    ![设置 Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. 在“概述”中，单击“评估和迁移服务器”。  
4. 在“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。  

    ![发现和评估服务器](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. 在“发现、评估和迁移服务器”中，单击“添加工具”。  
2. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。 
3. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置，然后单击“下一步”。  

    ![创建 Azure Migrate 项目](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    可在下述任一地理位置创建 Azure Migrate 项目。

    **地域** | **区域**
    --- | ---
    亚洲 | 东南亚
    欧洲 | 欧洲北部或欧洲西部
    美国 | 美国东部或美国中西部

    为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 可为实际迁移选择任一目标区域。
4. 在“选择评估工具”中，选择“暂时跳过添加评估工具” > “下一步”。   
5. 在“选择迁移工具”中，选择“Azure Migrate:   服务器迁移” > “下一步”。 
6. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。  
7. 添加工具后，它会显示在 Azure Migrate 项目的“服务器” > “迁移工具”中。  

## <a name="set-up-the-replication-appliance"></a>设置复制设备

第一个迁移步骤是设置复制设备。 复制设备是单个高度可用的本地 VMware VM，其中托管了以下组件：

- **配置服务器**：配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- **进程服务器**：进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 中的缓存存储帐户。 进程服务器还会将移动服务代理安装在要复制的 VM 上，并在本地 VMware VM 上执行自动发现。


若要设置复制设备，请下载已准备好的开放虚拟化应用程序 (.OVA) 模板。 将该模板导入 VMware，并创建复制设备 VM。 

### <a name="download-the-replication-appliance-template"></a>下载复制设备模板

按如下所述下载模板：

1. 在 Azure Migrate 项目中，单击“迁移目标”下的“服务器”。  
2. 在“Azure Migrate - 服务器” > “Azure Migrate:   服务器迁移”中，单击“发现”。 

    ![发现 VM](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. 在“发现计算机” > “计算机是否已虚拟化?”中，单击“是，使用 VMWare vSphere 虚拟机监控程序”。   
4. 在“想要如何迁移?”中，选择“使用基于代理的复制”。  
5. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。 
6. 选择“确认迁移的目标区域为 <区域名称>”。 
7. 单击“创建资源”。  随即会在后台创建一个 Azure Site Recovery 保管库。
    - 单击此按钮后，无法更改此项目的目标区域。
    - 所有后续迁移的目标都是此区域。

    ![创建恢复服务保管库](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. 在“是否安装新的复制设备?”中，选择“安装复制设备”。  
9. 单击“下载”以下载复制设备。  这会下载一个 OVF 模板，使用它可以创建运行该设备的新 VMware VM。
    ![下载 OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. 记下资源组和恢复服务保管库的名称。 在部署设备期间需要用到这些名称。


### <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

下载 OVF 模板后，将其导入 VMware，以便在运行 Windows Server 2016 的 VMware VM 上创建复制应用程序。

1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器或 vSphere ESXi 主机。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导    。 
3. 在“选择源”中，输入下载的 OVF 所在的位置  。
4. 在“查看详细信息”中，选择“下一步”。  
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置   。
6. 在“选择存储” > “选择虚拟磁盘格式”中，为获得最佳性能，请选择“Thick Provision Eager Zeroed”。   
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中，若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”    。

   > [!TIP]
   > 若要添加其他 NIC，请清除“部署后打开”   >   “完成”。 默认情况下，模板包含单个 NIC。 可以在部署后添加其他 NIC。

### <a name="kick-off-replication-appliance-setup"></a>启动复制设备的安装

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份使用管理员密码登录到 VM。
4. 首次登录时，会在数秒内启动复制应用程序设置工具（Azure Site Recovery 配置工具）。
5. 输入一个名称，用于向 Azure Migrate 服务器迁移注册设备。 然后单击“下一步”  。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅  。
7. 等待该工具完成注册 Azure AD 应用以标识设备。 设备将重新启动。
1. 再次登录到计算机。 在数秒内，配置服务器管理向导会自动启动。

### <a name="register-the-replication-appliance"></a>注册复制设备

完成复制设备的设置和注册。

1. 在配置服务器管理向导中，选择“设置连接”。 
2. 选择复制设备用来发现 VM 的 NIC（默认只有一个 NIC），并在源计算机上执行移动服务的推送安装。
3. 选择复制设备用来与 Azure 建立连接的 NIC。 再选择“保存”  。 配置后无法更改此设置。
4. 如果设备位于代理服务器后面，则你需要指定代理设置。
    - 将代理名称指定为 **http://ip-address** 或 **http://FQDN** 。 不支持 HTTPS 代理服务器。
5. 当系统提示你输入订阅、资源组和保管库详细信息时，请添加下载设备模板时记下的详细信息。
6. 在“安装第三方软件”中  ，接受许可协议。 选择“下载并安装”，安装 MySQL 服务器。 
7. 选择“安装 VMware PowerCLI”  。 执行此操作之前，请确保所有浏览器窗口已关闭。 然后选择“继续”。 
8. 在“验证设备配置”中验证先决条件，然后继续  。
9. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址  。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
10. 输入[创建](#prepare-an-account-for-automatic-discovery)的用于发现 VMware 的帐户的凭据。 选择“添加” > “继续”   。
11. 在“配置虚拟机凭据”中，输入在为 VM 启用复制时，为了推送安装移动服务而[创建](#prepare-an-account-for-mobility-service-installation)的凭据。   
    - 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。
    - 对于 Linux，请提供根帐户的详细信息。
12. 选择“完成配置”以完成注册  。


注册复制设备后，Azure Migrate 服务器评估将使用指定的设置连接到 VMware 服务器，并发现 VM。 可以在“其他”选项卡上的“管理” > “已发现的项”中查看已发现的 VM。   


## <a name="replicate-vms"></a>复制 VM

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate:   服务器迁移”，然后单击“复制”。 

    ![复制 VM](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化”中，选择“是，使用 VMware vSphere”。    
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称。 
4. 在“vCenter 服务器”中，指定用于管理 VM 的 vCenter 服务器的名称，或者 VM 所在的 vSphere 服务器的名称。 
5. 在“进程服务器”中，选择复制设备的名称。 
6. 在“来宾凭据”中，指定用于推送安装移动服务的 VM 管理员帐户。  然后单击“下一页:  虚拟机”。

    ![复制 VM](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. 在“虚拟机”中，选择要复制的计算机。 

    - 如果已针对 VM 运行评估，则可以应用评估结果中提供的 VM 大小和磁盘类型（高级/标准）建议。 为此，请在“从 Azure Migrate 评估导入迁移设置?”中选择“是”选项。  
    - 如果你未运行评估，或者不想要使用评估设置，请选择“否”选项。 
    - 如果你已选择使用评估，请选择 VM 组和评估名称。

8. 检查要迁移的每个 VM。 然后单击“下一页:  目标设置”。
9. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。 
10. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。 
11. 在“Azure 混合权益”中： 

    - 如果你不想要应用 Azure 混合权益，请选择“否”。  然后单击“下一步”  。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。  然后单击“下一步”  。

12. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性集。  VM 必须符合 [Azure 要求](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表将包含建议的大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。  
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性集**：如果迁移后 VM 应位于某个 Azure 可用性集中，请指定该集。 该集必须位于为迁移指定的目标资源组中。

13. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。  然后单击“下一步”  。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。 

14. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。  

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。   开始复制后无法更改设置。




## <a name="track-and-monitor"></a>跟踪和监视

- 单击“复制”会启动一个“开始复制”作业。  
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。


可以在门户通知中跟踪作业状态。

![跟踪作业](./media/tutorial-migrate-vmware-agent/jobs.png)

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器”   来监视复制状态。
![监视复制](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器迁移”中，单击“测试已迁移的服务器”。 

     ![测试已迁移的服务器](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。 

    ![测试迁移](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。  建议使用非生产 VNet。
4. “测试迁移”作业随即启动。  在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。  计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。  

    ![清理迁移](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate:   服务器迁移”，然后单击“复制服务器”。 

    ![复制服务器](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。  
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。    
    - 默认情况下，Azure Migrate 将关闭本地 VM，并运行按需复制，以同步自上次复制发生以来发生的任何 VM 更改。 这可以确保不会丢失数据。
    - 如果你不想要关闭 VM，请选择“否” 
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。 

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。  这会停止本地计算机的复制，并清理 VM 的复制状态信息。
2. 在已迁移的计算机上安装 Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 代理。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 从本地 VM 清单中删除本地 VM。
7. 从本地备份中删除本地 VM。
8. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)锁定和限制入站流量访问。
    - 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
-  考虑部署[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)。
