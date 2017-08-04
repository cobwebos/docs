---
title: "与经典 Windows VM 相关的技术文章 | Microsoft Azure"
description: "与经典部署模型中 Windows 虚拟机相关的 Microsoft Azure 文章完整列表"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-service-management
editor: 
ms.assetid: 7f9a508b-34ec-4bdb-92d1-8844480369d5
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 5c971ed2e09bd275dc34da3ea94b3ff4a6338c9a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---
# <a name="technical-articles-for-windows-vms-in-the-classic-deployment-model"></a>与经典部署模型中 Windows VM 相关的技术文章
查找在经典部署模型中创建和管理基于 Windows 的 Azure 虚拟机时需要参考的所有文档。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。

## <a name="overview"></a>概述
[关于虚拟机](windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[使用经典部署模型创建的 Azure 虚拟机的常见问题](windows/classic/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[VM、网站和云服务对比](../app-service-web/choose-web-site-cloud-service-vm.md)

[Azure 中的虚拟机和容器](windows/containers.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="environment-setup"></a>环境设置
[免费帐户](https://azure.microsoft.com/free/)

[安装 Azure PowerShell](/powershell/azure/overview)

[安装 Azure CLI](../cli-install-nodejs.md)

## <a name="get-started"></a>入门
[Windows VM 的学习路径](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)

[在 Azure 经典门户中创建 Windows 虚拟机](windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何登录到运行 Windows Server 的经典虚拟机](windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="plan"></a>计划
[关于经典虚拟机的映像](windows/classic/about-images.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[虚拟机的大小](windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[高性能计算 VM 大小](windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure 虚拟机的计划内维护](windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Azure 基础结构服务实施准则](windows/infrastructure-subscription-accounts-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[创建虚拟机的可用性集](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="deploy"></a>部署
[创建运行 Windows 的自定义虚拟机](windows/classic/createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[捕获经典部署模型中创建的 Windows 虚拟机](windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 PowerShell 创建和上传经典 Windows Server VHD](windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Chef 自动执行 Azure 虚拟机部署](windows/chef-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[在 Azure PowerShell 中创建和配置经典 Windows 虚拟机](windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[将自定义数据注入到 Azure 虚拟机中](windows/classic/inject-custom-data.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="manage"></a>管理
[使用 Azure PowerShell 管理虚拟机](windows/classic/manage-psh.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[将经典 VNet 连接到新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)

[关于虚拟机代理和扩展](windows/classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[管理虚拟机扩展](windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[适用于经典 Windows 虚拟机的自定义脚本扩展](windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[平台支持的从经典部署模型到 Azure Resource Manager 的迁移](windows/migration-classic-resource-manager-deep-dive.md)

## <a name="configure"></a>配置
[如何为 Windows VM 重置密码或远程桌面服务](windows/reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[关于虚拟机扩展和功能](windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在 Windows VM 上安装和配置 Trend Micro Deep Security 即服务](windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在经典部署模型中为虚拟机配置可用性集](windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在经典 Azure 虚拟机上设置终结点](windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="storage"></a>存储
[关于 Azure 虚拟机的磁盘和 VHD](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[如何将数据磁盘附加到经典 Windows 虚拟机](windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何从经典 Windows 虚拟机中分离数据磁盘](windows/classic/detach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 D 盘作为 Windows VM 上的数据驱动器](windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="networking"></a>联网
[虚拟网络概述](../virtual-network/virtual-networks-overview.md)

[将使用经典部署模型创建的虚拟机连接到虚拟网络或云服务](windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Azure PowerShell 管理 NSG](../virtual-network/virtual-networks-create-nsg-classic-ps.md)

[创建负载均衡器](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

## <a name="develop"></a>开发
[在 Visual Studio 中创建和管理 Azure 虚拟机](windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[使用 Visual Studio 创建用于Web 应用程序的虚拟机](windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[如何在虚拟机上通过 Java 运行需要进行大量计算的任务](windows/classic/java-run-compute-intensive-task.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Windows Server VM 上的 Django Hello World Web 应用程序](windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="workloads"></a>工作负荷
[HPC Pack](windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[MongoDB](windows/classic/install-mongodb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[MySQL](windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[Oracle](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)

[SAP](windows/classic/sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[SQL Server](./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[Tomcat](windows/classic/java-run-tomcat-app-server.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="reference"></a>引用
[服务管理模式下的 Azure CLI 命令](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)

[服务管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[服务管理 .NET API](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Azure 服务管理 PowerShell cmdlet 参考文档](/powershell/azure/overview?view=azuresmps-3.7.0)

## <a name="troubleshooting"></a>故障排除
[对运行 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[对在 Azure 虚拟机上运行的应用程序的访问进行故障排除](windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[在 Azure 中创建、重新启动 VM 或调整其大小时排查分配失败](windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[排查在 Azure 中新建 Windows 虚拟机时遇到的经典部署问题](windows/classic/troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

[排查在 Azure 中重新启动或调整现有 Windows 虚拟机时遇到的经典部署问题](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)

