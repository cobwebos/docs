---
title: 在 Azure 中排查 OpenShift 部署问题 | Microsoft Docs
description: 在 Azure 中排查 OpenShift 部署问题。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: f94189e8f39771e0c3d7856ccbb8a23ba81d94aa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447268"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>在 Azure 中排查 OpenShift 部署问题

如果 OpenShift 群集未成功部署，Azure 门户将提供错误输出。 该输出可能难以阅读，导致难以识别问题。 请在此输出中快速扫描退出代码 3、4 或 5。 下面提供了有关这三个退出代码的信息：

- 退出代码 3:Red Hat 订阅用户名 / 密码或组织 ID / 激活密钥不正确
- 退出代码 4:在 Red Hat 池 ID 不正确或不没有可用的任何权利
- 退出代码 5:未能预配 Docker 精简池卷

对于其他所有退出代码，请通过 SSH 连接到主机，以查看日志文件。

**OpenShift 容器平台**

通过 SSH 连接到 ansible playbook 主机。 对于模板或市场套餐，请使用守护主机。 在守护主机中，可以通过 SSH 连接到群集中的其他所有节点（主节点、infra、CNS、计算节点）。 需要以 root 用户身份查看日志文件。 默认禁止 root 进行 SSH 访问，因此，请不要使用 root 身份通过 SSH 连接到其他节点。

**OKD**

通过 SSH 连接到 ansible playbook 主机。 对于 OKD 模板（3.9 和更低版本），请使用 master-0 主机。 对于 OKD 模板（3.10 和更高版本），请使用守护主机。 在 ansible playbook 主机中，可以通过 SSH 连接到群集中的其他所有节点（主节点、infra、CNS、计算节点）。 需要以 root 用户 (sudo su -) 身份查看日志文件。 默认禁止 root 进行 SSH 访问，因此，请不要使用 root 身份通过 SSH 连接到其他节点。

## <a name="log-files"></a>日志文件

主机准备脚本的日志文件（stderr 和 stdout）位于所有主机的 /var/lib/waagent/custom-script/download/0 中。 如果在准备主机期间出错，请查看这些日志文件以确定错误。

如果准备脚本成功运行，则需要检查 ansible playbook 主机的 /var/lib/waagent/custom-script/download/1 目录中的日志文件。 如果在实际安装 OpenShift 期间出错，stdout 文件将显示错误。 使用此信息来联系支持人员，以获得进一步的帮助。

示例输出

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

安装期间最常见的错误包括：

1. 私钥包含通行短语
2. 未正确创建包含私钥的 Key Vault 机密
3. 输入的服务主体凭据不正确
4. 服务主体对资源组没有参与者访问权限

### <a name="private-key-has-a-passphrase"></a>私钥包含通行短语

将会出现 SSH 权限被拒绝的错误。 通过 SSH 连接到 ansible playbook 主机，以检查私钥中的通行短语。

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>未正确创建包含私钥的 Key Vault 机密

私钥已注入到 ansible playbook 主机 - ~/.ssh/id_rsa。 确认此文件正确。 通过与 ansible playbook 主机中的某个群集节点建立 SSH 会话来进行测试。

### <a name="service-principal-credentials-were-entered-incorrectly"></a>输入的服务主体凭据不正确

向模板或市场套餐提供输入时，提供了错误的信息。 请确保为服务主体使用正确的 appId (clientId) 和密码 (clientSecret)。 通过发出以下 Azure CLI 命令进行验证。

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>服务主体对资源组没有参与者访问权限

如果启用了 Azure 云提供程序，则使用的服务主体必须对资源组拥有参与者访问权限。 通过发出以下 Azure CLI 命令进行验证。

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>其他工具

对于某些错误，还可以使用以下命令获取详细信息：

1. systemctl status <service>
2. journalctl -xe
