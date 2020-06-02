---
title: Azure 快速入门 - 使用 Desired State Configuration 配置 VM | Microsoft Docs
description: 本文可帮助你开始使用 Desired State Configuration 配置 VM。
services: automation
ms.subservice: dsc
keywords: dsc, 配置, 自动化
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 28e8f921fa7620d1fec7dec1788ed769026624d7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836713"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>使用 Desired State Configuration 配置 VM

启用 Azure 自动化 State Configuration 后，可以使用 Desired State Configuration (DSC) 来管理和监视 Windows 和 Linux 服务器的配置。 可以识别或自动更正不符合所需配置的配置。 本快速入门介绍如何使用 Azure 自动化 State Configuration 启用 Linux VM 和部署 LAMP 堆栈。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
* 运行 Red Hat Enterprise Linux、CentOS 或 Oracle Linux 的 Azure 资源管理器 VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>登录 Azure
通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-a-virtual-machine"></a>启用一个虚拟机

可以通过多种不同的方法启用用于 State Configuration 功能的计算机。 本快速入门介绍如何使用自动化帐户为 VM 启用此功能。 若要详细了解如何使用不同的方法为计算机启用 State Configuration，请阅读[启用通过 Azure 自动化 State Configuration 执行管理任务的计算机](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)。

1. 在 Azure 门户的左窗格中，选择“自动化帐户”。 如果该选项未显示在左侧窗格中，请单击“所有服务”，然后在出现的视图中进行搜索。
1. 在列表中选择一个自动化帐户。
1. 在自动化帐户的左窗格中，选择“状态配置(DSC)”。
2. 单击“添加”以打开“VM 选择”页。
3. 查找要为其启用 DSC 的虚拟机。 可以使用搜索栏和筛选器选项来查找特定的虚拟机。
4. 单击该虚拟机，然后单击“连接”。
5. 选择适用于虚拟机的 DSC 设置。 如果已准备好一个配置，可将其指定为 `Node Configuration Name`。 可以将[配置模式](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig)设置为控制计算机的配置行为。
6. 单击“确定”。 将 DSC 扩展部署到虚拟机时，状态将显示为 `Connecting`。

![启用用于 DSC 的 Azure VM](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>导入模块

模块包含 DSC 资源，在 [PowerShell 库](https://www.powershellgallery.com)中可以找到许多模块。 在配置中使用的任何资源都必须在编译之前导入到自动化帐户中。 本教程需要名为 **nx** 的模块。

1. 在自动化帐户的左侧窗格中，在“共享资源”下选择“模块库”。 
1. 通过键入模块的部分名称来搜索要导入的模块：`nx`。
1. 单击要导入的模块。
1. 单击“导入”。

![导入 DSC 模块](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>导入配置

本快速入门使用 DSC 配置在计算机上配置 Apache HTTP Server、MySQL 和 PHP。 参阅 [DSC 配置](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations)。

在文本编辑器中键入以下内容，并在本地将文件保存为 AMPServer.ps1。

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

若要导入配置，请执行以下操作：

1. 在自动化帐户的左窗格中，选择“状态配置(DSC)”，然后单击“配置”选项卡。
2. 单击“+ 添加”。
3. 选择在前一步骤中保存的配置文件。
4. 单击“确定”。

## <a name="compile-a-configuration"></a>编译配置

必须先将 DSC 配置编译为节点配置（MOF 文档），然后才能将其分配到节点。 编译时会验证配置，并允许输入参数值。 若要详细了解如何编译配置，请参阅[编译 State Configuration 中的配置](automation-dsc-compile.md)。

1. 在自动化帐户的左窗格中，选择“状态配置(DSC)”，然后单击“配置”选项卡。
1. 选择配置 `LAMPServer`。
1. 在菜单选项中选择“编译”，然后单击“是”。 
1. 在“配置”视图中，你可以看到新的编译作业已排队。 成功完成作业以后，即可转到下一步。 如果发生任何失败，可以单击编译作业来查看详细信息。

## <a name="assign-a-node-configuration"></a>分配节点配置

可将已编译的节点配置分配到 DSC 节点。 可以通过分配操作将配置应用到计算机，并监视或自动更正不符合配置的任何情况。

1. 在自动化帐户的左侧窗格中，选择“State Configuration (DSC)”，然后单击“节点”选项卡。 
1. 选择要将配置分配到的节点。
1. 单击“分配节点配置”
1. 选择节点配置 `LAMPServer.localhost`，并单击“确定”。 State Configuration 现在会将已编译的配置分配到该节点，而节点状态将更改为 `Pending`。 下一次进行定期检查时，节点会检索该配置，应用该配置并报告状态。 节点最长可能需要花费 30 分钟时间来检索配置，具体时间取决于节点设置。 
1. 若要强制进行即时检查，可以在 Linux 虚拟机上以本地方式运行以下命令：`sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![分配节点配置](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>查看节点状态

可以查看你的自动化帐户中所有 State Configuration 托管节点的状态。 选择“State Configuration (DSC)”并单击“节点”选项卡会显示此信息。 可以通过状态、节点配置或名称搜索的方式来筛选显示结果。

![DSC 节点状态](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已启用一个用于 State Configuration 的 Linux VM，为 LAMP 堆栈创建了一个配置，并将该配置部署到了该 VM。 若要了解如何使用 Azure 自动化 State Configuration 启用持续部署，请继续阅读以下文章：

> [!div class="nextstepaction"]
> [使用 Chocolatey 设置持续部署](./automation-dsc-cd-chocolatey.md)