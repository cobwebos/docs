---
title: 排查到 Azure Stack 的 Kubernetes 部署 |Microsoft Docs
description: 了解如何排查到 Azure Stack 的 Kubernetes 部署。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 472dfc04cea65cab39d177bb214c417d229b71d2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956714"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>排查部署到 Kubernetes (K8) 以及向 Azure Stack 部署 Kubernetes (K8) 的问题

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。

以下文章讨论了如何排查 Kubernetes 群集问题。 可以按部署所需的元素查看部署警报以及部署的状态。 您可能需要从 Azure Stack 或 Linux Vm 托管 Kubernetes 收集部署日志。 您可能还需要与 Azure Stack 管理员联系，以从管理终结点检索日志。

## <a name="overview-of-deployment"></a>部署概述

在开始故障排除群集之前，你可能想要查看 Azure Stack Kubernetes 群集部署过程。 部署使用 Azure 资源管理器解决方案模板创建 Vm 并安装你的群集 ACS 引擎。

### <a name="deployment-workflow"></a>部署工作流

下图显示了用于部署群集的一般过程。

![部署 Kubernetes 过程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 从 marketplace 项收集输入的参数。

    输入创建 Kubernetes 群集，所需的值包括：
    -  **用户名称**： 是的 Kubernetes 群集和 DVM 一部分的 Linux 虚拟机的用户名。
    -  **SSH 公钥**： 用于为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机的授权密钥。
    -  **服务主体**: Kubernetes Azure 云提供程序使用的 ID。 标识为应用程序 ID 时创建服务主体客户端 ID。 
    -  **客户端机密**： 它们主要在创建服务主体时创建。

2. 创建部署 VM 和自定义脚本扩展。
    -  通过使用 marketplace Linux 映像创建 Linux VM 的部署**Ubuntu Server 16.04 LTS**。
    -  下载并运行应用商店中的客户脚本扩展。 该脚本**Linux 2.0 的自定义脚本**。
    -  运行 DVM 自定义脚本。 该脚本会执行以下任务：
        1. 从 Azure 资源管理器元数据终结点中获取库终结点。
        2. 获取 Azure 资源管理器元数据终结点从 active directory 资源 ID。
        3. 为 ACS 引擎加载 API 模型。
        4. 将 ACS 引擎部署到 Kubernetes 群集，并将保存到 Azure Stack 云配置文件`/etc/kubernetes/azurestackcloud.json`。
3. 创建主 Vm。

4. 下载并运行客户脚本扩展。

5. 运行主脚本。

    该脚本会执行以下任务：
    - 安装 etcd、Docker 和 Kubernetes 资源（如 kubelet）。 etcd 是一种分布式键值存储，用于跨一组计算机存储数据。 Docker 支持称为容器基本操作系统级虚拟化。 Kubelet 是在每个 Kubernetes 节点上运行的节点代理。
    - 设置 etcd 服务。
    - 设置了 kubelet 服务。
    - 启动 kubelet。 此任务涉及以下步骤：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
6. 创建代理虚拟机。

7. 下载并运行客户脚本扩展。

7. 运行代理脚本。 代理自定义脚本执行以下任务：
    - 安装 etcd
    - 设置 kubelet 服务
    - 加入 Kubernetes 群集

## <a name="steps-for-troubleshooting"></a>故障排除步骤

支持在 Kubernetes 群集的虚拟机上，可以收集日志。 还可以查看部署日志。 您可能需要与你的 Azure Stack 管理员若要验证 Azure Stack，若要使用，以及若要从与你的部署相关的 Azure Stack 中获取日志所需的版本。

1. 审阅[部署状态](#review-deployment-status)并[检索日志](#get-logs-from-a-vm)在 Kubernetes 群集中的主节点。
2. 请确保使用 Azure Stack 的最新版本。 如果不确定正在使用哪个版本，请联系你的 Azure Stack 管理员。 Kubernetes 群集 marketplace 时 0.3.0 需要 1808年或更高版本的 Azure Stack 版本。
3.  查看 VM 创建文件。 您可能已具有以下问题：  
    - 公钥可能无效。 查看你创建的密钥。  
    - 创建 VM 可能会触发了内部错误，或触发创建错误。 许多因素可能会导致错误，包括 Azure Stack 订阅的容量限制。
    - 请确保 VM 的完全限定的域名 (FDQN) 开头重复的前缀。
4.  如果 VM 位于**确定**，然后评估此 DVM。 如果 DVM 有一条错误消息：

    - 公钥可能无效。 查看你创建的密钥。  
    - 需要联系你的 Azure Stack 管理员使用特权终结点适用于 Azure Stack 检索日志。 有关详细信息，请参阅 [Azure Stack 诊断工具](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics)。
5. 如果您有关于你的部署的任何问题，您可以将其发布，或者查看如果有人已回答了问题中的[Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。 

## <a name="review-deployment-status"></a>查看部署状态

当部署 Kubernetes 群集时，可以查看部署状态，检查存在任何问题。

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。
2. 选择**资源组**，然后选择部署 Kubernetes 群集时使用的资源组的名称。
3. 选择**部署**，然后选择**部署名称**。

    ![故障排除](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  查看故障排除窗口。 每个已部署的资源提供了以下信息：
    
    | 属性 | 说明 |
    | ----     | ----        |
    | 资源 | 资源的名称。 |
    | 类型 | 资源提供程序和资源的类型。 |
    | 状态 | 项状态。 |
    | 时间戳 | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，例如操作、 资源终结点和资源的名称中涉及的资源提供程序。 |

    每个项都有绿色或红色状态的图标。

## <a name="get-logs-from-a-vm"></a>从 VM 获取日志

若要生成日志，你需要连接到主 VM 群集，打开 bash 提示符，然后运行脚本。 主 VM 可在群集资源组中，并且名为`k8s-master-<sequence-of-numbers>`。 

### <a name="prerequisites"></a>必备组件

需要一个 bash 提示用于管理 Azure Stack 的计算机上。 使用 bash 可运行访问日志的脚本。 Windows 上，可以使用与 Git 一起安装在 bash 提示符处。 若要获取最新版本的 git，请参阅[Git 下载](https://git-scm.com/downloads)。

### <a name="get-logs"></a>获取日志

若要获取日志，请执行以下步骤：

1. 打开 bash 提示符。 如果您使用的 Git 的 Windows 计算机上，可以从以下路径打开 bash 提示符： `c:\programfiles\git\bin\bash.exe`。
2. 运行以下 bash 命令：

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > 在 Windows 中，您不需要运行`sudo`。 相反，您可以只使用`chmod 744 getkuberneteslogs.sh`。

3. 在同一会话中，以更新，以匹配你的环境的参数运行以下命令：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. 检查参数，并设置基于你的环境的值。
    | 参数           | 说明                                                                                                      | 示例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i、--identity-file | RSA 私钥文件将 Kubernetes 主 VM 连接。 该密钥必须以 `-----BEGIN RSA PRIVATE KEY-----` 开头 | C:\data\privatekey.pem                                                        |
    | -h、--host          | 公共 IP 或 Kubernetes 群集主 VM 的完全限定的域名 (FQDN)。 该 VM 名称以 `k8s-master-` 开头。                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u、--user          | Kubernetes 群集主 VM 的用户名。 配置 marketplace 项时设置此名称。                                                                    | azureuser                                                                     |
    | -d、--vmdhost       | 公共 IP 或 FQDN DVM。 该 VM 名称以 `vmd-` 开头。                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   当您添加参数值时，它可能看起来如以下代码：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    如果运行成功，则会创建日志。

    ![生成的日志](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. 检索由该命令创建的文件夹中的日志。 该命令将创建新的文件夹和它们进行时间戳。
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[（适用于 Azure Stack 操作员） 添加到 Marketplace 的 Kubernetes 群集](../azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
