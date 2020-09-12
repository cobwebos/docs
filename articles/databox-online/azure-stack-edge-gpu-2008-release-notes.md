---
title: Azure Stack Edge 预览版发行说明 |Microsoft Docs
description: 描述运行正式发行版的 Azure Data Box Gateway 的重要打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/04/2020
ms.author: alkohli
ms.openlocfilehash: 66cfda5ed83e52882dffe0d7507015f2405785c1
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500209"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>带有 GPU 预览发行说明的 Azure Stack 边缘

以下发行说明通过 GPU 识别适用于2008预览 Azure Stack 版本的预览版的重大问题和解决问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Azure Stack Edge 设备之前，请仔细查看发行说明中包含的信息。

本文适用于以下软件版本- **Azure Stack Edge 2008**。 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>新增功能

在 Azure Stack Edge 2008 版本中添加了以下新功能。 根据你所运行的特定预览版软件版本，你可能会看到这些功能的子集。 

- **存储类** -在以前的版本中，你只能通过 SMB 或 NFS 共享来静态设置存储，以便在 Azure Stack Edge 设备上运行的 Kubernetes 群集上部署的有状态应用程序。 在此版本中，添加了用于动态预配存储的存储类。 有关详细信息，请参阅 [Azure Stack EDGE GPU 设备上的 Kubernetes 存储管理](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)。 
- **具有指标服务器的 Kubernetes 仪表板** -在此版本中，添加了一个指标服务器加载项的 Kubernetes 仪表板。 你可以使用仪表板来大致了解 Azure Stack Edge 设备上运行的应用程序、查看 Kubernetes 群集资源的状态，并查看设备上发生的任何错误。 指标服务器将 CPU 和内存使用量聚合到设备上的 Kubernetes 资源。 有关详细信息，请参阅 [使用 Kubernetes 仪表板监视 Azure Stack 边缘 GPU 设备](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。
- **适用于 Azure Stack Edge 的 Azure Arc** -从此版本开始，可以通过 Azure Arc 在 Azure Stack Edge 设备上部署应用程序工作负载。Azure Arc 是一种混合管理工具，可用于在 Kubernetes 群集上部署应用程序。 有关详细信息，请参阅 [通过 Azure Arc 在 Azure Stack Edge 设备上部署工作负荷](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。  

## <a name="known-issues"></a>已知问题 

下表提供了 Azure Stack Edge 设备的已知问题的摘要。

| 否。 | 功能 | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge + Azure SQL | 创建 SQL 数据库需要管理员访问权限。   |执行以下步骤，而不是中的步骤 1-2 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) 。 <ul><li>在设备的本地 UI 中，启用 "计算接口"。 选择 **计算 > 端口号 > 启用计算 > 应用。**</li><li>`sqlcmd`从下载客户端计算机https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>连接到计算接口 IP 地址 () 启用的端口，并将 "，1401" 添加到地址的末尾。</li><li>最终命令如下所示： sqlcmd-S {Interface IP}，1401-U SA-P "强！Passw0rd "。</li>完成此操作后，当前文档中的步骤3-4 应相同。 </li></ul> |
| **2.** |刷新| 不支持对通过 **刷新** 还原的 blob 进行增量更改 |对于 Blob 终结点，刷新后对 blob 进行部分更新可能导致更新不会上载到云中。 例如，一系列操作，例如：<ul><li>在云中创建 blob。 或从设备中删除之前上传的 blob。</li><li>使用刷新功能，将 blob 从云中刷新到设备中。</li><li>使用 Azure SDK REST Api 仅更新部分 blob。</li></ul>这些操作可能导致 blob 的更新部分不在云中更新。 <br>**解决方法**：使用 robocopy 等工具或通过资源管理器或命令行的常规文件复制来替换整个 blob。|
|**3.**|限制|在限制期间，如果不允许新写入设备，NFS 客户端完成的写入会失败并出现 "权限被拒绝" 错误。| 错误如下所示：<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir：无法创建目录 "test"：权限被拒绝|
|**4.**|Blob 存储引入|使用 AzCopy 版本10进行 Blob 存储引入时，请使用以下参数运行 AzCopy： `Azcopy <other arguments> --cap-mbps 2000`| 如果没有为 AzCopy 提供这些限制，则可能会将大量请求发送到设备，并导致服务出现问题。|
|**5.**|分层存储帐户|使用分层存储帐户时，以下内容适用：<ul><li> 仅支持块 blob。 页 blob 不受支持。</li><li>没有快照或复制 API 支持。</li><li> 不支持通过 Hadoop 工作负荷引入 `distcp` ，因为它会大量使用复制操作。</li></ul>||
|**共.**|NFS 共享连接|如果将多个进程复制到相同的共享，并且 `nolock` 未使用该属性，则在复制过程中可能会出现错误。|`nolock`必须将属性传递给 mount 命令，以将文件复制到 NFS 共享。 例如：`C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`。|
|**全天候.**|Kubernetes 群集|在运行 kubernetes 群集的设备上应用更新时，kubernetes 虚拟机将重新启动并重新启动。 在这种情况下，只会在更新后自动还原使用指定副本部署的 pod。  |如果在未指定副本集的情况下在复制控制器之外创建了单独的 pod，则在设备更新后，将不会自动还原这些 pod。 需要还原这些 pod。<br>出于任何原因（例如节点故障或中断性节点升级），副本集会替换删除或终止的 pod。 出于此原因，我们建议你使用副本集，即使你的应用程序只需要一个 pod。|
|**8.**|Kubernetes 群集|仅 Helm v3 或更高版本支持 Azure Stack Edge 上的 Kubernetes。 有关详细信息，请参阅 [常见问题解答：删除 Tiller](https://v3.helm.sh/docs/faq/)。|
|**900.**|Azure Arc + Azure Stack 边缘|如果在 Azure Stack Edge 设备上配置 web 代理，则不支持 Azure Arc 部署。||
|**万.**|Kubernetes |端口31000保留给 Kubernetes 仪表板。 同样，在默认配置中，IP 地址172.28.0.1 和172.28.0.10 分别保留用于 Kubernetes 服务和核心 DNS 服务。|不要使用保留 Ip。|
|**11x17.**|Kubernetes |Kubernetes 当前不允许多协议 LoadBalancer 服务。 例如，需要同时侦听 TCP 和 UDP 的 DNS 服务。 |若要解决 Kubernetes 与 MetalLB 的这一限制，两个服务 (一个用于 TCP，一个用于 UDP) ，可以在同一 pod 选择器上创建一个。 这些服务使用相同的共享密钥和 loadBalancerIP 来共享相同的 IP 地址。 如果服务数多于可用 IP 地址，则还可以共享 Ip。 <br> 有关详细信息，请参阅 [IP 地址共享](https://metallb.universe.tf/usage/#ip-address-sharing)。|
|**10.**|Kubernetes 群集|现有 Azure IoT Edge marketplace 模块将不会在 Kubernetes 群集上作为 Azure Stack Edge 设备上 IoT Edge 的托管平台运行。|在将这些模块部署到 Azure Stack Edge 设备上之前，需要对其进行修改。 有关详细信息，请参阅修改 marketplace Azure IoT Edge 模块，使其在 Azure Stack Edge 设备上运行。<!-- insert link-->|
|**9.**|Kubernetes |Azure Stack Edge 设备上的 Kubernetes 上的 Azure IoT Edge 不支持基于文件的绑定装入。|IoT Edge 使用转换层将选项转换 `ContainerCreate` 为 Kubernetes 构造。 `Binds`不能将映射创建到 hostpath 目录，也不能创建基于文件的绑定装载，因此无法将其绑定到 IoT Edge 容器中的路径。|
|**毫米.**|Kubernetes |如果为 IoT Edge 提供自己的证书，并将其添加到 Azure Stack Edge 设备上，则不会在 Helm 图更新过程中选取新证书。|若要解决此问题，请 [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md)。 重新启动 `iotedged` 和 pod `edgehub` 。|
|**15.**|证书 |在某些情况下，本地 UI 中的证书状态可能需要几秒钟才能更新。 |本地 UI 中的以下方案可能会受到影响。<ul><li>"**证书**" 页中的 "**状态**" 列。</li><li>"**入门**" 页中的 "**安全**" 磁贴。</li><li>**概述**页中的 "**配置**" 磁贴。</li></ul>  |

## <a name="next-steps"></a>后续步骤

- [准备部署带 GPU Azure Stack Edge 设备](azure-stack-edge-gpu-deploy-prep.md)

