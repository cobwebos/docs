---
title: 在不使用预配代理的情况下创建 Linux 映像
description: 在 Azure 中创建不含预配代理的通用 Linux 映像。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: d177e7fd7d18b24f9d8fd7f3e6662abe16bba317
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045325"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>在不使用预配代理的情况下创建一般化映像

Microsoft Azure 以[walinuxagent](https://github.com/Azure/WALinuxAgent)或[云初始化](https://github.com/canonical/cloud-init)的形式提供适用于 Linux vm 的预配代理（推荐）。 但是，如果你不希望为预配代理使用这两个应用程序，则可能会出现这种情况，例如：

- 你的 Linux 发行版/版本不支持 cloud-init/Linux 代理。
- 需要设置特定的 VM 属性，例如主机名。

> [!NOTE] 
>
> 如果不需要设置任何属性或发生任何形式的预配，应考虑创建专用映像。

本文介绍如何设置 VM 映像以满足 Azure 平台要求并设置主机名，而无需安装预配代理。

## <a name="networking-and-reporting-ready"></a>网络和报告就绪

为了使 Linux VM 与 Azure 组件通信，你需要使用 DHCP 客户端从虚拟网络中检索主机 IP 以及 DNS 解析和路由管理。 大多数发行版附带了这些实用程序。 Linux 发行版供应商在 Azure 上进行了测试的工具 `dhclient` 包括 `network-manager` 、 `systemd-networkd` 和其他。

> [!NOTE]
>
> 当前在没有预配代理的情况下创建通用化映像仅支持已启用 DHCP 的 Vm。

设置并配置网络后，必须 "报告就绪"。 这会告知 Azure VM 已成功预配。

> [!IMPORTANT]
>
> 未能向 Azure 报告就绪将导致重新启动 VM！

## <a name="demosample"></a>演示/示例

此演示将演示如何获取现有 Marketplace 映像（在本例中为 Debian Buster VM）并删除 Linux 代理（walinuxagent），同时创建最基本的过程来向 Azure 报告 VM 已 "就绪"。

### <a name="create-the-resource-group-and-base-vm"></a>创建资源组和基本 VM：

```bash
$ az group create --location eastus --name demo1
```

创建基 VM：

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

VM 预配后，可以通过 SSH 连接到该 VM 并删除 Linux 代理：

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>向 VM 添加所需代码

也可以在 VM 内部，因为我们已删除 Azure Linux 代理，需要提供一种机制来报告就绪。 

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

如果 VM 未安装 Python 或不可用，则可以通过以下步骤以编程方式重现以上脚本逻辑：

1. `ContainerId` `InstanceId` 通过分析 WireServer 中的响应来检索和： `curl -X GET -H 'x-ms-version: 2012-11-30' http://$168.63.129.16/machine?comp=goalstate` 。

2. 构造以下 XML 数据，注入 `ContainerId` `InstanceId` 以上步骤中分析的和：
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

此演示使用 systemd，这是新式 Linux 发行版中最常见的初始化系统。 因此，最简单且最常使用的方法是确保在适当的时间运行此报表就绪机制，以创建 systemd 服务单元。 你可以将以下单位文件添加到 `/etc/systemd/system` （此示例为单位文件命名 `azure-provisioning.service` ）：

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

1. 报告已准备好到 Azure （以指示它已成功）。
1. 通过从 IMDS 拉取此数据，从用户提供的 VM 名称重命名 VM。
1. 禁用自身，使其仅在首次启动时运行，而不会在后续重新启动时运行。

在文件系统上，运行以下内容以启用该单元：

```
$ sudo systemctl enable azure-provisioning.service
```

现在 VM 已准备好进行通用化，并创建了一个映像。 

#### <a name="completing-the-preparation-of-the-image"></a>完成映像准备工作

返回开发计算机，运行以下内容来准备从基本 VM 创建映像：

```
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

并通过此 VM 创建映像：

```
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

现在，我们已准备好从映像创建新的 VM （或多个 Vm）：

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
> 必须将设置为， `--enable-agent` `false` 因为要从映像创建的 VM 上不存在 walinuxagent。

此 VM 应成功预配。 登录到新预配 VM 后，应该可以看到 report ready systemd service 的输出：

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

如果你实现自己的预配代码/代理，则你将拥有此代码的支持，Microsoft 支持将仅调查与预配接口不可用相关的问题。 我们会不断改进和更改此领域，因此你必须监视云初始化和 Azure Linux 代理中的更改，以设置 API 更改。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[Linux 预配](provisioning.md)。
