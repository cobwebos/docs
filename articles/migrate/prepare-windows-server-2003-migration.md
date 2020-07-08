---
title: 为迁移准备 Windows Server 2003 服务器 Azure Migrate
description: 了解如何为迁移准备 Windows Server 2003 服务器，并 Azure Migrate。
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 33519764b138c7711e6c03a85aa33ec6f936a748
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84172122"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>为迁移准备 Windows Server 2003 计算机

本文介绍如何准备运行 Windows Server 2003 的计算机迁移到 Azure。 

- 你可以使用无代理迁移将[Hyper-v vm](tutorial-migrate-hyper-v.md)和[VMware Vm](tutorial-migrate-vmware.md)迁移到 Azure。
- 若要在迁移后连接到 Azure Vm，Hyper-v Integration Services 必须安装在 Azure VM 上。 默认情况下，Windows Server 2003 计算机未安装此安装程序。
- 没有直接下载链接可用于安装 Hyper-v Integration Services，因此，你需要执行以下操作：
    - 对于未安装它的 Hyper-v Vm，请在运行带有 Hyper-v 角色的 Windows Server 2012 R2/Windows Server 2012 的计算机上提取 Integration Services 的安装文件，然后将该安装程序复制到 Windows Server 2003 计算机。 安装文件在运行 Windows Server 2016 的计算机上不可用。
    - 对于 VMware Vm，请创建一个启动任务，该任务将在迁移后启动 Azure VM 时安装 Integration Services。


## <a name="install-on-hyper-v-vms"></a>在 Hyper-v Vm 上安装

迁移之前，请检查是否已安装 Hyper-v Integration Services，并根据需要进行安装。

1. 按照[这些说明](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager)检查是否已安装。
2. 如果未安装，请使用 Hyper-v 角色登录到运行 Windows Server 2012 R2/Windows Server 2012 的计算机。
3. 导航到**C:\Windows\System32\vmguest.iso**上的安装文件，并装载该文件。
2. 将安装文件夹复制到 Windows Server 2003 计算机上，然后安装 Integration Services。
4. 安装后，可以在 Integration Services 中保留默认设置。 

## <a name="install-on-vmware-vms"></a>在 VMware Vm 上安装

1. 使用 Hyper-v 角色登录到运行 Windows Server 2012 R2/Windows Server 2012 的计算机。
2. 导航到**C:\Windows\System32\vmguest.iso**上的安装文件，并装载该文件。
3. 将安装文件夹复制到 VMware VM。
4. 从 VM 上的命令行运行 ```gpedit.msc``` 。
5. 打开 "**计算机配置**  >  **Windows 设置**  >  **脚本（启动/关机）**"。
6. 在 "**启动**"  >  "**添加**  >  **脚本名称**" 中，键入 setup.exe 地址。
7. 迁移到 Azure 后，该脚本将在 Azure VM 首次启动时运行。
8. 手动重新启动 Azure VM。 启动诊断弹出窗口指示需要重新启动。
9. 脚本运行后，Hyper-v Integration Services 安装在 Azure VM 上，你可以从启动中删除该脚本。
10. 安装后，可以在 Integration Services 中保留默认设置。 

## <a name="next-steps"></a>后续步骤

- 查看[VMware](migrate-support-matrix-vmware-migration.md)和[hyper-v](migrate-support-matrix-hyper-v-migration.md) vm 的迁移要求。
- 迁移[VMware](server-migrate-overview.md)和[hyper-v](tutorial-migrate-hyper-v.md) vm。
