---
title: Azure Stack Edge 预览版发行说明 |Microsoft Docs
description: 描述运行正式发行版的 Azure Data Box Gateway 的重要打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 6c29240aa3267cd93ba0c3de1f0c797ce1a1483c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083611"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>带有 GPU 预览发行说明的 Azure Stack 边缘

以下发行说明通过 GPU 识别适用于2008预览 Azure Stack 版本的预览版的重大问题和解决问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Azure Stack Edge 设备之前，请仔细查看发行说明中包含的信息。

此 **Azure Stack 2008** 版与以下软件版本相对应：

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>GA 版本中的已知问题

下表提供了 Azure Stack Edge 设备的已知问题的摘要。

| 不是。 | Feature | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge + Azure SQL | 创建 SQL 数据库需要管理员访问权限。   |执行以下步骤，而不是中的步骤 1-2 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) 。 <ul><li>在设备的本地 UI 中，启用 "计算接口"。 选择 **计算 > 端口号 > 启用计算 > 应用。**</li><li>在连接到设备的 PowerShell 窗口中，运行 `Add-HcsComputeNetwork` 。 </li><li>`sqlcmd`从下载客户端计算机https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>连接到计算接口 IP 地址 () 启用的端口，并将 "，1401" 添加到地址的末尾。</li><li>最终命令如下所示： sqlcmd-S {Interface IP}，1401-U SA-P "强！Passw0rd "。</li>完成此操作后，当前文档中的步骤3-4 应相同。 </li></ul> |
| **2.** |刷新| 不支持对通过 **刷新** 还原的 blob 进行增量更改 |对于 Blob 终结点，刷新后对 blob 进行部分更新可能导致更新不会上载到云中。 例如，一系列操作，例如：<ul><li>在云中创建 blob。 或从设备中删除之前上传的 blob。</li><li>使用刷新功能，将 blob 从云中刷新到设备中。</li><li>使用 Azure SDK REST Api 仅更新部分 blob。</li></ul>这些操作可能导致 blob 的更新部分不在云中更新。 <br>**解决方法**：使用 robocopy 等工具或通过资源管理器或命令行的常规文件复制来替换整个 blob。|
|**3.**|限制|在限制期间，如果不允许新写入设备，NFS 客户端完成的写入会失败并出现 "权限被拒绝" 错误。| 错误如下所示：<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir：无法创建目录 "test"：权限被拒绝|
|**4.**|Blob 存储引入|使用 AzCopy 版本10进行 Blob 存储引入时，请使用以下参数运行 AzCopy： `Azcopy <other arguments> --cap-mbps 2000`| 如果没有为 AzCopy 提供这些限制，则可能会将大量请求发送到设备，并导致服务出现问题。|
|**5.**|分层存储帐户|使用分层存储帐户时，以下内容适用：<ul><li> 仅支持块 blob。 页 blob 不受支持。</li><li>没有快照或复制 API 支持。</li><li> 不支持通过 Hadoop 工作负荷引入 `distcp` ，因为它会大量使用复制操作。</li></ul>||
|**共.**|NFS 共享连接|如果将多个进程复制到相同的共享，并且 `nolock` 未使用该属性，则在复制过程中可能会出现错误。|`nolock`必须将属性传递给 mount 命令，以将文件复制到 NFS 共享。 例如：`C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`。|
|**全天候.**|Kubernetes 群集|在运行 kubernetes 群集的设备上应用更新时，kubernetes 虚拟机将重新启动并重新启动。 在这种情况下，只会在更新后自动还原使用指定副本部署的 pod。  |如果在未指定副本集的情况下在复制控制器之外创建了单独的 pod，则在设备更新后，将不会自动还原这些 pod。 需要还原这些 pod。<br>出于任何原因（例如节点故障或中断性节点升级），副本集会替换删除或终止的 pod。 出于此原因，我们建议你使用副本集，即使你的应用程序只需要一个 pod。|
|**8.**|Kubernetes 群集|仅 Helm v3 或更高版本支持 Azure Stack Edge 上的 Kubernetes。 有关详细信息，请参阅 [常见问题解答：删除 Tiller](https://v3.helm.sh/docs/faq/)。|
|**900.**|Azure Arc + Azure Stack 边缘|如果在 Azure Stack Edge 设备上配置 web 代理，则不支持 Azure Arc 部署。||
|**万.**|Kubernetes |端口31000保留给 Kubernetes 仪表板。 同样，在默认配置中，IP 地址10.96.0.1 和10.96.0.10 分别保留用于 Kubernetes 服务和核心 DNS 服务。|不要使用保留 Ip。|
|**11x17.**|Kubernetes |Kubernetes 当前不允许多协议 LoadBalancer 服务。 例如，需要同时侦听 TCP 和 UDP 的 DNS 服务。 |若要解决 Kubernetes 与 MetalLB 的这一限制，两个服务 (一个用于 TCP，一个用于 UDP) ，可以在同一 pod 选择器上创建一个。 这些服务使用相同的共享密钥和 loadBalancerIP 来共享相同的 IP 地址。 如果服务数多于可用 IP 地址，则还可以共享 Ip。 <br> 有关详细信息，请参阅 [IP 地址共享](https://metallb.universe.tf/usage/#ip-address-sharing)。|
|**10.**|Kubernetes 群集|现有 Azure IoT Edge marketplace 模块将不会在 Kubernetes 群集上作为 Azure Stack Edge 设备上 IoT Edge 的托管平台运行。|在将这些模块部署到 Azure Stack Edge 设备上之前，需要对其进行修改。 有关详细信息，请参阅修改 marketplace Azure IoT Edge 模块，使其在 Azure Stack Edge 设备上运行。<!-- insert link-->|
|**9.**|Kubernetes |Azure Stack Edge 设备上的 Kubernetes 上的 Azure IoT Edge 不支持基于文件的绑定装入。|IoT Edge 使用转换层将选项转换 `ContainerCreate` 为 Kubernetes 构造。 `Binds`不能将映射创建到 hostpath 目录，也不能创建基于文件的绑定装载，因此无法将其绑定到 IoT Edge 容器中的路径。|


## <a name="next-steps"></a>后续步骤

- [准备部署带 GPU Azure Stack Edge 设备](azure-stack-edge-gpu-deploy-prep.md)

