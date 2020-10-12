---
title: 创建不含预配代理的 Linux 映像
description: 在 Azure 中创建不含预配代理的通用 Linux 映像。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 63bc3caf97e1325c365171ba3f8e6353885d9b68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322545"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>创建不含预配代理的通用映像

Microsoft Azure 提供适用于 Linux Vm 的预配代理，其形式为 " [walinuxagent](https://github.com/Azure/WALinuxAgent) " 或 " [云初始化](https://github.com/canonical/cloud-init) (建议) "。 但在某些情况下，你不希望使用这些应用程序之一作为你的预配代理，例如：

- 你的 Linux 发行版/版本不支持 cloud-init/Linux 代理。
- 你需要设置特定的 VM 属性，例如主机名。

> [!NOTE] 
>
> 如果你不需要设置任何属性，也不需要进行任何形式的预配，则应考虑创建专用映像。

本文介绍了在不安装预配代理的情况下如何设置 VM 映像来满足 Azure 平台要求并设置主机名。

## <a name="networking-and-reporting-ready"></a>联网并报告就绪状态

为了使 Linux VM 与 Azure 组件通信，你需要使用 DHCP 客户端从虚拟网络中检索主机 IP，并且还需要 DNS 解析和路由管理。 大多数发行版都附带了这些实用工具。 Linux 发行版供应商已在 Azure 上进行了测试的工具包括 `dhclient`、`network-manager`、`systemd-networkd`，等等。

> [!NOTE]
>
> 当前仅启用了 DHCP 的 VM 支持创建不含预配代理的通用映像。

设置并配置网络后，必须“报告就绪状态”。 这将告诉 Azure 该 VM 已成功预配。

> [!IMPORTANT]
>
> 不能向 Azure 报告就绪状态将导致 VM 重启！

## <a name="demosample"></a>演示/示例

此演示将展示如何获取现有市场映像（在本例中为 Debian Buster VM）并删除 Linux 代理 (walinuxagent)，同时创建最基本的流程来向 Azure 报告 VM 已“就绪”。

### <a name="create-the-resource-group-and-base-vm"></a>创建资源组和基本 VM：

```bash
$ az group create --location eastus --name demo1
```

创建基本 VM：

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>删除映像预配代理

预配 VM 后，可以通过 SSH 连接到该 VM 并删除 Linux 代理：

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>向 VM 添加所需的代码

还是在 VM 内，因为我们已删除了 Azure Linux 代理，所以我们需要提供一种机制来报告就绪状态。 

#### <a name="python-script"></a>Python 脚本

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>一般步骤（不使用 Python）

如果 VM 未安装或未提供 Python，则可通过以下步骤以编程方式重现以上脚本逻辑：

1. 通过分析来自 WireServer 的响应检索 `ContainerId` 和 `InstanceId`：`curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate`。

2. 构造以下 XML 数据，注入在上面的步骤分析的 `ContainerId` 和 `InstanceId`：
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. 将此数据发布到 WireServer：`curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>首次启动时自动运行代码

此演示使用 systemd，这是新式 Linux 发行版中最常见的初始化系统。 因此，要确保报告就绪状态的此机制在正确的时间运行，最简单且最原始的方法是创建一个 systemd 服务单元。 你可以将以下单元文件添加到 `/etc/systemd/system`（此示例将单元文件命名为 `azure-provisioning.service`）：

```
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

此 systemd 服务在基本预配过程中执行以下三项操作：

1. 向 Azure 报告就绪状态（指示它已成功启动）。
1. 通过从 [Azure 实例元数据服务 ](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service)将此数据提取 (IMDS) ，基于用户提供的 vm 名称重命名 VM。 **注意** IMDS 还提供其他 [实例元数据](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#accessing-azure-instance-metadata-service)（如 SSH 公钥），因此你可以设置多个主机名。
1. 禁用此服务自身，以使其仅在首次启动时运行，在后续启动时不运行。

将此单元添加到文件系统后，运行以下命令来启用它：

```
$ sudo systemctl enable azure-provisioning.service
```

现在 VM 已准备好进行通用化，并基于它创建了一个映像。 

#### <a name="completing-the-preparation-of-the-image"></a>完成映像准备工作

返回到开发计算机，运行以下命令，准备通过基本 VM 创建映像：

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

基于此 VM 创建映像：

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

现在，我们已准备好基于映像创建一个新的 VM（或多个 VM）：

```
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> 将 `--enable-agent` 设置为 `false` 非常重要，因为要基于映像创建的这个 VM 上不存在 walinuxagent。

此 VM 应当会成功预配。 登录到新预配的 VM 后，应该能够看到报告就绪状态的 systemd 服务的输出：

```
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>支持

如果你实现自己的预配代码/代理，你将负责支持此代码，Microsoft 支持人员将仅调查与预配接口不可用相关的问题。 我们在不断改进和更改此领域，因此你必须监视 cloud-init 中的更改和 Azure Linux 代理中的预配 API 更改。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Linux 预配](provisioning.md)。
