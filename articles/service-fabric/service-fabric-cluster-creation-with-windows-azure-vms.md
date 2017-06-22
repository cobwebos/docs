---
title: "使用运行 Windows 的 Azure VM 创建独立群集 | Microsoft 文档"
description: "了解如何创建和管理运行 Windows Server 的 Azure 虚拟机上的 Azure Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 7eeb40d2-fb22-4a77-80ca-f1b46b22edbc
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: ryanwi;chackdan
redirect_url: /azure/service-fabric/service-fabric-cluster-creation-via-arm
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ba1f6e5662700c309fcf198a4e114fd9b2b47c5f
ms.contentlocale: zh-cn
ms.lasthandoff: 03/30/2017


---
# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>使用运行 Windows Server 的 Azure 虚拟机创建三节点独立 Service Fabric 群集
本文介绍如何使用用于 Windows Server 的独立 Service Fabric 安装程序在基于 Windows 的 Azure 虚拟机 (VM) 上创建群集。 此方案是一种[创建和管理在 Windows Server 上运行的群集](service-fabric-cluster-creation-for-windows-server.md)的特殊情况，其中 VM 是[运行 Windows Server 的 Azure VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，但不会创建[基于云的 Azure Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)。 此模式的区别在于根据以下步骤创建的独立 Service Fabric 群集由你完全管理，而基于云的 Azure Service Fabric 群集则由 Service Fabric 资源提供程序管理和升级。

## <a name="steps-to-create-the-standalone-cluster"></a>创建独立群集的步骤
1. 登录到 Azure 门户，在资源组中创建新的 Windows Server 2012 R2 Datacenter VM 或 Windows Server 2016 Datacenter VM。 有关更多详细信息，请阅读[在 Azure 门户中创建 Windows VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 一文。
2. 将多个 VM 添加到同一资源组。 确保每个 VM 在创建后都有相同的管理员用户名和密码。 创建以后，你会在同一虚拟网络中看到所有三个 VM。
3. 使用[服务器管理器的“本地服务器”仪表板](https://technet.microsoft.com/library/jj134147.aspx)连接到每个 VM 并关闭 Windows 防火墙。 这确保网络流量可以在计算机之间通信。 同时连接到每台计算机，通过打开命令提示符并键入 `ipconfig` 来获取 IP 地址。 或者可以在门户中查看每台计算机的 IP 地址，方法是选择资源组的虚拟网络资源，并检查为每台计算机创建的网络接口。
4. 连接到其中一台 VM，测试是否可以成功地 ping 其他两台 VM。
5. 连接到其中一台 VM，并将 [Windows Server 独立 Service Fabric 包下载](http://go.microsoft.com/fwlink/?LinkId=730690)到此虚拟机的新文件夹中，然后提取该包。
6. 在记事本中打开 *ClusterConfig.Unsecure.MultiMachine.json* 文件，使用计算机的三个 IP 地址编辑每个节点。 更改顶部的群集名称，然后保存文件。  下方显示了群集清单的部分示例。
   
    ```
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "01-2017",
        "nodes": [
        {
            "nodeName": "standalonenode0",
            "iPAddress": "10.1.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "standalonenode1",
            "iPAddress": "10.1.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "standalonenode2",
            "iPAddress": "10.1.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
        ],
    ```
7. 若要确保群集的安全性，请确定要采取的措施，并按照以下关联链接中的步骤执行操作：[X509 证书](service-fabric-windows-cluster-x509-security.md)或 [Windows 安全性](service-fabric-windows-cluster-windows-security.md)。 如果使用 Windows 安全性设置群集，则需要设置域控制器以管理 Active Directory。 请注意，不支持将域控制器计算机用作 Service Fabric 节点。
8. 打开 [PowerShell ISE 窗口](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。 导航到已在其中提取已下载的独立安装程序包并保存群集配置文件的文件夹。 运行以下 PowerShell 命令部署群集：
   
    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
    ```

此脚本将远程配置 Service Fabric 群集，应在部署进行时报告进度。

9. 大约一分钟后，可以使用某个计算机 IP 地址（例如通过使用 `http://10.1.0.5:19080/Explorer/index.html`）连接到 Service Fabric Explorer，以便检查群集是否正常运行。 

## <a name="next-steps"></a>后续步骤
* [在 Windows Server 或 Linux 上创建独立的 Service Fabric 群集](service-fabric-deploy-anywhere.md)
* [在独立 Service Fabric 群集中添加或删除节点](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [独立 Windows 群集的配置设置](service-fabric-cluster-manifest.md)
* [使用 Windows 安全性保护 Windows 上的独立群集](service-fabric-windows-cluster-windows-security.md)
* [使用 X509 证书保护 Windows 上的独立群集](service-fabric-windows-cluster-x509-security.md)


