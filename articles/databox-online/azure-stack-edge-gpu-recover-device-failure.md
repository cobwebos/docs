---
title: 从 Azure Stack Edge Pro 设备故障中恢复
description: 描述如何从 Azure Stack Edge Pro 故障设备进行恢复。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ef99ff874376e0d76e45e4b0b2c36a78d938130b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843761"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>从失败的 Azure Stack Edge Pro GPU 设备恢复 

本文介绍如何在 Azure Stack Edge Pro GPU 设备上从不可承受的故障中恢复。 Azure Stack Edge Pro GPU 设备上无法容忍的故障需要更换设备。

## <a name="before-you-begin"></a>在开始之前

确保你有：

- 已与有关设备故障的 Microsoft 支持部门联系，并已建议更换设备。 
- 备份设备配置，如 [准备设备故障](azure-stack-edge-gpu-prepare-device-failure.md)中所述。


## <a name="configure-replacement-device"></a>配置替换设备

当设备遇到不可容忍的故障时，需要订购更换设备。 更换设备的配置步骤保持不变。 

检索从失败的设备备份的设备配置信息。 你将使用此信息来配置替换设备。  

请按照以下步骤配置更换设备：

1. 收集 [部署清单](azure-stack-edge-gpu-deploy-checklist.md)中所需的信息。 你应使用先前设备配置中保存的信息。 
1. 将同一配置的新设备与失败的设备进行排序。  若要下订单，请在 Azure 门户中 [创建新的 Azure Stack Edge 资源](azure-stack-edge-gpu-deploy-prep.md#) 。
1. [解包](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)、将设备 [安装到机架上](azure-stack-edge-gpu-deploy-install.md#rack-the-device) 并 [连接设备](azure-stack-edge-gpu-deploy-install.md#cable-the-device)。 
1. [连接到设备的本地 UI](azure-stack-edge-gpu-deploy-connect.md)。
1. 使用用于旧设备的相同 IP 地址来配置网络。 这会最大程度地减少对环境中使用的任何客户端计算机的影响。 请参阅如何 [配置网络设置](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
1. 分配与旧设备相同的设备名称和 DNS 域。 这可确保客户端可以使用相同的设备名称与新设备通信。 请参阅如何 [配置设备设置](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。
1. 采用与旧设备相同的方式在新设备上配置证书。 请记住，新设备有一个新的节点序列号。 如果在旧设备上使用了自己的证书，则需要获取新的节点证书。 请参阅如何 [配置证书](azure-stack-edge-gpu-deploy-configure-certificates.md)。
1. 从 Azure 门户获取激活密钥并激活新设备。 请参阅如何 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。

你现在已准备好部署在旧设备上运行的工作负荷。

## <a name="restore-edge-cloud-shares"></a>还原边缘云共享

请按照以下步骤操作，在设备上还原 Edge 云共享上的数据：

1. [添加](azure-stack-edge-j-series-manage-shares.md#add-a-share) 以前在失败设备上创建的共享名称相同的共享。 请确保在创建共享时， **选择 "blob 容器** " 设置为 " **使用现有** 选项"，然后选择与先前设备一起使用的容器。
1. [添加](azure-stack-edge-j-series-manage-users.md#add-a-user) 有权访问上一设备的用户。
1. 添加与先前设备上的共享相关联的[存储帐户](azure-stack-edge-j-series-manage-storage-accounts.md#add-an-edge-storage-account)。 创建边缘存储帐户时，请选择 "从现有容器" 并指向映射到在上一设备上映射的 Azure 存储帐户的容器。 写入先前设备上的边缘存储帐户的设备中的所有数据都已上传到映射的 Azure 存储帐户中的选定存储容器。
1. [刷新共享](azure-stack-edge-j-series-manage-shares.md#refresh-shares) Azure 中的数据。 这会将现有容器中的所有云数据提取到共享中。

## <a name="restore-edge-local-shares"></a>还原边缘本地共享

若要为潜在设备故障做好准备，你可能已部署以下备份解决方案，以保护 Kubernetes 或 IoT 工作负荷中的本地共享数据：

| 第三方软件           | 对解决方案的引用                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 有关详细信息，请联系 Cohesity。          |
| Veritas                        | 有关详细信息，请与 Veritas 联系。   |

完全配置替换设备后，启用设备进行本地存储。 

请按照以下步骤从本地共享恢复数据： 

1. [在设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. [添加本地共享](azure-stack-edge-j-series-manage-shares.md#add-a-local-share) 。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的引用。

## <a name="restore-vm-files-and-folders"></a>还原 VM 文件和文件夹

若要为潜在设备故障做好准备，你可能已部署以下备份解决方案之一来保护 Vm 上的数据：



| 备份解决方案        | 支持的 OS   | 参考                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| 用于 Azure 备份的 (MARS) 代理 Microsoft Azure 恢复服务 | Windows        | [关于 MARS 代理](/azure/backup/backup-azure-about-mars)    |
| Cohesity                | Windows、Linux | [Microsoft Azure 集成、备份和恢复解决方案简述](https://www.cohesity.com/solution/cloud/azure) <br>有关详细信息，请联系 Cohesity。                          |
| Veritas                 | Windows、Linux | 有关详细信息，请与 Veritas 联系。                    |

完全配置替换设备后，你可以使用以前使用的 VM 映像重新部署 Vm。 

请按照以下步骤来恢复 Vm 中的数据：
 
1. 从设备上的[vm 映像部署 vm](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 。 
1. 在 VM 上安装所选的数据保护解决方案。
1. 运行所选的数据保护解决方案提供的恢复过程。 请参阅上表中的引用。

## <a name="next-steps"></a>后续步骤

- 了解如何 [返回 Azure Stack Edge Pro 设备](azure-stack-edge-return-device.md)。
