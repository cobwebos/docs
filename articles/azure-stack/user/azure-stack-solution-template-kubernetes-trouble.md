---
title: 你部署到 Kubernetes (K8) 到 Azure Stack 故障排除 |Microsoft Docs
description: 了解如何在部署到 Kubernetes (K8) 到 Azure Stack 故障排除。
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
ms.date: 10/11/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: fbb51d8dc3b1ea4c6b34120e8fe35474ae949cf2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116906"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>排查到 Azure Stack 的 Kubernetes 部署

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 处于预览状态。

以下文章讨论了 Kubernetes 群集进行疑难解答。 您可以查看部署警报以及部署的状态部署所需的元素。 您可能需要从 Azure Stack 或 Linux Vm 托管 Kubernetes 收集部署日志。 此外，您可能需要与 Azure Stack 管理员联系，以从管理终结点检索日志。

## <a name="overview-of-deployment"></a>部署概述

获取为步骤来解决你的群集之前，你可能想要查看 Azure Stack Kubernetes 群集部署过程。 部署使用 Azure 资源管理器解决方案模板来创建 Vm 并安装 ACS 引擎的群集。

### <a name="deployment-workflow"></a>部署工作流

下图显示了用于部署群集的一般过程。

![部署 Kubernetes 进程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 收集输入参数与 marketplace 项。

    输入所需设置 Kubernetes 群集包括的值：
    -  **用户名称**是 Kubernetes 群集的一部分的 Linux 虚拟机以及 DVM 的用户名称。
    -  **SSH 公钥**用于到 Kubernetes 群集和 DVM 的一部分创建的所有 Linux 计算机的授权的密钥
    -  **服务主体**Kubernetes Azure 云提供程序所使用的 ID。 标识为应用程序 ID 创建服务主体时客户端 ID。 
    -  **客户端机密**密钥的创建服务主体时创建它们。

2. 创建 VM 的部署和自定义脚本扩展。
    -  创建部署 Linux VM 使用 marketplace 的 Linux 映像**Ubuntu Server 16.04 LTS**。
    -  下载并执行从 marketplace 客户脚本扩展。 该脚本**Linux 2.0 的自定义脚本**。
    -  在运行 DVM 自定义脚本。 脚本：
        1. 从 Azure 资源管理器元数据终结点中获取库终结点。
        2. 获取 Azure 资源管理器元数据终结点从 active directory 资源 ID。
        3. 将为 ACS 引擎加载 API 模型。
        4. 将 ACS 引擎部署到 Kubernetes 群集，并保存到的 Azure Stack 云配置文件`/etc/kubernetes/azurestackcloud.json`。
3. 创建主 Vm。

    下载并执行客户脚本扩展。

4. 在运行主脚本。

    脚本：
    - 安装 etcd、 Docker 和 Kubernetes kubelet 等资源。 etcd 是分布式的键值存储，提供跨计算机群集的存储数据的方式。 Docker 支持基本操作系统级别 virtualizations 称为容器。 Kubelet 是在每个 Kubernetes 节点运行的节点代理。
    - 设置了 etcd 服务。
    - 设置了 Kubelet 服务。
    - 启动 kubelet。 这涉及以下过程：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
5. 创建代理的 Vm。

    下载并执行客户脚本扩展。

6. 运行代理的脚本。 代理自定义脚本：
    - 安装 etcd。
    - 设置 Kubelet 服务。
    - 加入 Kubernetes 群集。

## <a name="steps-for-troubleshooting"></a>故障排除步骤

可以在支持 Kubernetes 群集 Vm 上收集日志。 此外可以查看部署日志。 您可能还需要与你的 Azure Stack 管理员来验证使用的，Azure Stack 的版本并从与你的部署相关的 Azure Stack 获取日志。

1. 审阅[部署状态](#review-deployment-status)并[检索日志](#get-logs-from-a-vm)在 Kubernetes 群集中的主节点。
2. 需要使用 Azure Stack 的最新版本。 如果您不确定你的 Azure Stack 版本，请联系 Azure Stack 管理员。 Kubernetes 群集 marketplace 时 0.3.0 需要 1808年或更高版本的 Azure Stack 版本。
3.  查看你的 VM 创建文件。 您可能会遇到以下问题：  
    - 公钥可能无效。 查看已创建的密钥。  
    - 创建 VM 可能会触发了内部错误，或触发创建错误。 由多种因素，包括 Azure Stack 订阅的容量限制可能会导致错误。
    - VM 的完全限定的域名 (FDQN) 是否开头重复的前缀？
4.  如果 VM 位于**确定**，，评估此 DVM。 如果此 DVM 具有一条错误消息：

    - 公钥可能无效。 查看已创建的密钥。  
     - 你将需要与 Azure Stack 管理员联系，以检索 Azure Stack 使用特权终结点的日志。 有关详细信息，请参阅[Azure Stack 诊断工具](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics)。
5. 如果你的部署有疑问，可以发布你的问题或请参阅如果有人已回答了问题中的[Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。 

## <a name="review-deployment-status"></a>查看部署状态

部署 Kubernetes 群集可查看任何问题时，可以查看部署状态。

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。
2. 选择**资源组**，然后选择资源组的名称时使用部署 Kubernetes 群集。
3. 选择**部署**，然后**部署名称**。

    ![故障排除](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  请参阅故障排除窗口中。 每个已部署的资源提供了以下信息。
    
    | 属性 | 说明 |
    | ----     | ----        |
    | 资源 | 资源的名称。 |
    | Type | 资源提供程序和资源的类型。 |
    | 状态 | 将项的状态。 |
    | 时间戳 | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，例如操作、 资源终结点和资源的名称中所涉及的资源提供程序。 |

    每个项都将有绿色或红色状态的图标。

## <a name="get-logs-from-a-vm"></a>从 VM 获取日志

你将需要连接到主 VM 群集，打开的 bash 提示符处，并运行一个脚本来生成日志。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 

### <a name="prerequisites"></a>必备组件

你将需要一个 bash 提示在计算机上使用来管理 Azure Stack。 使用 bash 运行访问的日志的脚本。 Windows 上，可以使用与 Git 一起安装在 bash 提示符处。 若要获取最新版本的 git，请参阅[git 下载](https://git-scm.com/downloads)。

### <a name="get-logs"></a>获取日志

1. 打开 bash 提示符。 如果在 Windows 计算机上使用 git，可以从以下路径打开 bash 提示符： `c:\programfiles\git\bin\bash.exe`。
2. 运行以下 bash 命令：

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > 在 Windows 中，您不需要运行`sudo`和可以只需使用`chmod 744 getkuberneteslogs.sh`。

3. 在同一会话中，以更新，以匹配你的环境的参数运行以下命令。

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    检查参数并设置基于你的环境的值。
    | 参数           | 说明                                                                                                      | 示例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i、-标识文件 | 要连接 kubernetes 主 VM 的 RSA 私钥文件。 密钥必须开头 `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h、--主机          | 公共 ip 或 Kubernetes 群集主 VM 的完全限定的域名 (FQDN)。 VM 名称开头`k8s-master-`。                       | IP: 192.168.102.37<br><br>FQDN: k8s 12345.local.cloudapp.azurestack.external      |
    | -u，--用户          | Kubernetes 群集主 VM 的用户名。 配置 marketplace 项时设置此名称。                                                                    | azureuser                                                                     |
    | -d、-vmdhost       | 公共 ip 或 FQDN DVM。 VM 名称开头`vmd-`。                                                       | IP: 192.168.102.38<br><br>DNS: vmd dnsk8 frog.local.cloudapp.azurestack.external |

   当您添加参数值时，它可能如下所示：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    如果运行成功创建日志。

    ![生成的日志](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. 检索由该命令创建的文件夹中的日志。 该命令将创建一个新文件夹和时间戳它。
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine kubernetes dvm.log

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)。

[将 Kubernetes （适用于 Azure Stack 操作员） 添加到 Marketplace](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
