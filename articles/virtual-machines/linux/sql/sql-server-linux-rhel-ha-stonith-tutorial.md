---
title: 在 Azure 中的 RHEL 虚拟机上为 SQL Server 配置可用性组 - Linux 虚拟机 | Microsoft Docs
description: 了解如何在 RHEL 群集环境中设置高可用性和设置 STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: 3f5f51c944cab84c00e29b2f72433a726ed70d33
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024341"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>教程：在 Azure 中的 RHEL 虚拟机上为 SQL Server 配置可用性组 

> [!NOTE]
> 演示的教程为**公共预览版**。 
>
> 本教程结合使用 SQL Server 2017 和 RHEL 7.6，但也可以使用 RHEL 7 或 RHEL 8 中的 SQL Server 2019 来配置 HA。 用于配置可用性组资源的命令在 RHEL 8 中已更改，有关正确命令的详细信息，请查看[创建可用性组资源](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) 和 RHEL 8 资源。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 创建新的资源组、可用性集和 Azure Linux 虚拟机 (VM)
> - 启用高可用性 (HA)
> - 创建 Pacemaker 群集
> - 通过创建 STONITH 设备来配置隔离代理
> - 在 RHEL 上安装 SQL Server 和 mssql-tools
> - 配置 SQL Server Always On 可用性组
> - 在 Pacemaker 群集中配置可用性组 (AG) 资源
> - 测试故障转移和隔离代理

本教程将使用 Azure 命令行接口 (CLI) 在 Azure 中部署资源。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果你希望在本地安装并使用 CLI，则本文需要 Azure CLI 版本 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组。

如果你有多个订阅，请[设置](/cli/azure/manage-azure-subscriptions-azure-cli)要将这些资源部署到的订阅。

使用以下命令在某个区域中创建资源组 `<resourceGroupName>`。 请将 `<resourceGroupName>` 替换为所选的名称。 本教程将使用 `East US 2`。 有关详细信息，请参阅以下[快速入门](../quick-create-cli.md)。

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>创建可用性集

下一步是创建可用性集。 在 Azure Cloud Shell 中运行以下命令（请将 `<resourceGroupName>` 替换为你的资源组名称）。 选择 `<availabilitySetName>` 的名称。

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

该命令完成后，应会获得以下结果：

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>在可用性集中创建 RHEL VM

> [!WARNING]
> 如果选择即用即付 (PAYG) RHEL 映像并配置高可用性 (HA)，可能需要注册订阅。 这可能会导致为订阅付费两次，因为既需要支付 VM 的 Microsoft Azure RHEL 订阅费，也需要支付 Red Hat 的订阅费。 有关详细信息，请参阅 https://access.redhat.com/solutions/2458541 。
>
> 为了避免“双重计费”，请在创建 Azure VM 时使用 RHEL HA 映像。 作为 RHEL-HA 映像提供的映像也是预先启用了 HA 存储库的 PAYG 映像。

1. 获取提供已启用 HA 的 RHEL 的虚拟机 (VM) 映像列表：

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    应该看到以下结果：

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    本教程将选择映像 `RedHat:RHEL-HA:7.6:7.6.2019062019`。

    > [!IMPORTANT]
    > 若要设置可用性组，计算机名称必须短于 15 个字符。 用户名不能包含大写字符，密码必须包含 12 个以上的字符。

1. 我们希望在可用性集中创建 3 个 VM。 请替换以下命令中的以下占位符：

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` - 例如“Standard_D16_v3”
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

上述命令将创建 VM，并为这些 VM 创建默认的 VNet。 有关不同配置的详细信息，请参阅 [az vm create](https://docs.microsoft.com/cli/azure/vm) 一文。

针对每个 VM 完成该命令后，应会获得如下所示的结果：

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> 默认情况下，使用上述命令创建的默认映像会创建 32GB OS 磁盘。 使用此默认安装时，有可能会耗尽空间。 例如，可将以下参数添加到上述 `az vm create` 命令，以创建具有 128GB 空间的 OS 磁盘：`--os-disk-size-gb 128`。
>
> 然后，如果需要扩展相应的文件夹卷来容纳安装，可以[配置逻辑卷管理器 (LVM)](../../../virtual-machines/linux/configure-lvm.md)。

### <a name="test-connection-to-the-created-vms"></a>测试与创建的 VM 的连接

在 Azure Cloud Shell 中使用以下命令连接到 VM1 或其他 VM。 如果找不到 VM IP，请按照这篇[有关 Azure Cloud Shell 的快速入门](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)进行操作。

```azurecli-interactive
ssh <username>@publicipaddress
```

如果连接成功，应会看到以下表示 Linux 终端内容的输出：

```output
[<username>@<VM1> ~]$
```

键入 `exit` 退出 SSH 会话。

## <a name="enable-high-availability"></a>启用高可用性

> [!IMPORTANT]
> 若要完成本教程部分，必须拥有 RHEL 和“高可用性”加载项的订阅。 如果使用上一部分推荐的映像，则无需注册其他订阅。
 
连接到每个 VM 节点并按照下面的指导启用 HA。 有关详细信息，请参阅[为 RHEL 启用高可用性订阅](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)。

> [!TIP]
> 如果同时与每个 VM 建立 SSH 会话，则操作会变得更容易，因为在整篇文章中，都需要在每个 VM 上运行相同的命令。
>
> 如果复制并粘贴多个 `sudo` 命令，并按提示输入密码，则其他命令不会运行。 请单独运行每个命令。


1. 在每个 VM 上运行以下命令以打开 Pacemaker 防火墙端口：

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. 使用以下命令在所有节点上更新并安装 Pacemaker 包：

    > [!NOTE]
    > **nmap** 作为此命令块的一部分安装，它是一个用于在网络中查找可用 IP 地址的工具。 不一定要安装 **nmap**，但稍后在本教程中，此工具会很有帮助。

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. 为安装 Pacemaker 包时创建的默认用户设置密码。 在所有节点上使用相同的密码。

    ```bash
    sudo passwd hacluster
    ```

1. 使用以下命令打开 hosts 文件，并设置主机名解析。 有关配置 hosts 文件的详细信息，请参阅[配置 AG](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites)。

    ```
    sudo vi /etc/hosts
    ```

    在 **vi** 编辑器中，输入 `i` 以插入文本，并在空白行中添加相应 VM 的**专用 IP**。 然后，在 IP 旁边的空格后面添加 VM 名称。 每行只能包含一个单独的条目。

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > 建议使用上述**专用 IP** 地址。 在此配置中使用公共 IP 地址会导致安装失败，并且我们不建议向外部网络公开 VM。

    若要退出 **vi** 编辑器，请先按 **Esc** 键，然后输入命令 `:wq` 以写入文件并退出。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集

在本部分，我们将启用并启动 pcsd 服务，然后配置群集。 对于 Linux 上的 SQL Server，不会自动创建群集资源。 需要手动启用并创建 Pacemaker 资源。 有关详细信息，请参阅有关[为 RHEL 配置故障转移群集实例](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)的文章

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>启用并启动 pcsd 服务和 Pacemaker

1. 在所有节点上运行命令。 重新启动后，这些命令可让节点重新加入群集。

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. 从所有节点中删除所有现有群集配置。 运行以下命令：

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. 在主要节点上，运行以下命令以设置群集。

    - 运行 `pcs cluster auth` 命令来对群集节点进行身份验证时，系统会提示输入密码。 请输入前面创建的 **hacluster** 用户的密码。

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. 运行以下命令，检查所有节点是否处于联机状态。

    ```bash
    sudo pcs status
    ```

    如果所有节点都处于联机状态，你将看到如下所示的输出：

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. 将活动群集中的预期投票设置为 3。 此命令仅影响活动群集，而不会更改配置文件。

    在所有节点上，使用以下命令设置预期投票：

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>配置隔离代理

STONITH 设备提供隔离代理。 以下说明已根据本教程进行修改。 有关详细信息，请参阅[创建 STONITH 设备](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device)。
 
[检查 Azure 隔离代理的版本以确保它已更新](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation)。 使用以下命令：

```bash
sudo yum info fence-agents-azure-arm
```

应会看到类似于以下示例的输出。

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册新应用程序
 
 1. 转到 https://portal.azure.com
 2. 打开[“Azure Active Directory”边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)。 转到“属性”并记下目录 ID。 此 ID 为 `tenant ID`
 3. 单击[ **“应用注册”** ](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)。
 4. 单击“新建注册”。 
 5. 输入一个**名称**（例如 `<resourceGroupName>-app`），选择“仅限此组织目录中的帐户” 
 6. 选择“Web”作为应用程序类型，输入登录 URL（例如 http://localhost) ，然后单击“添加”。  不会使用登录 URL，可为它输入任何有效的 URL
 7. 选择“证书和机密”，然后单击“新建客户端机密”  
 8. 输入新密钥（客户端机密）的说明，选择“永不过期”，然后单击“添加”  
 9. 请记下机密的值。 此值用作服务主体的密码
10. 选择“概述”。  记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的“登录 ID”）
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>为隔离代理创建自定义角色

按照[使用 Azure CLI 为 Azure 资源创建自定义角色](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role)教程进行操作。

JSON 文件的内容类似于：

- 将 `<username>` 替换为所选名称。 这是为了避免在创建此角色定义时出现任何重复。
- 请将 `<subscriptionId>` 替换为你的 Azure 订阅 ID。

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

若要添加角色，请运行以下命令：

- 请将 `<filename>` 替换为该文件的名称。
- 如果执行该命令所在的路径不是该文件所保存到的文件夹，请在该命令中包含该文件的文件夹路径。

```bash
az role definition create --role-definition "<filename>.json"
```

应该会看到以下输出：

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>将自定义角色分配给服务主体

将在上一步骤中创建的自定义角色 `Linux Fence Agent Role-<username>` 分配给服务主体。 不要再使用“所有者”角色！
 
1. 转到 https://portal.azure.com
2. 打开[“所有资源”边栏选项卡](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. 选择第一个群集节点的虚拟机
4. 单击“访问控制(IAM)” 
5. 单击“添加角色分配” 
6. 从“角色”列表中选择角色 `Linux Fence Agent Role-<username>` 
7. 在“选择”列表中，输入前面创建的应用程序名称 `<resourceGroupName>-app` 
8. 单击“保存” 
9. 针对所有群集节点重复上述步骤。

### <a name="create-the-stonith-devices"></a>创建 STONITH 设备

在节点 1 上运行以下命令：

- 将 `<ApplicationID>` 替换为应用程序注册中的 ID 值。
- 将 `<servicePrincipalPassword>` 替换为客户端机密中的值。
- 将 `<resourceGroupName>` 替换为用于本教程的订阅中的资源组。
- 替换 Azure 订阅中的 `<tenantID>` 和 `<subscriptionId>`。

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

由于我们已将一个允许 HA 服务 (`--add-service=high-availability`) 的规则添加到防火墙，因此无需在所有节点上打开以下防火墙端口：2224、3121、21064、5405。 但是，如果 HA 遇到任何类型的连接问题，请使用以下命令打开这些与 HA 关联的端口。

> [!TIP]
> 可以选择性地在本教程中一次性添加所有端口，以节省时间。 以下相关部分将会解释需要打开的端口。 如果你现在就要添加所有端口，请添加额外的端口：1433 和 5022。

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>安装 SQL Server 和 mssql-tools
 
参考以下部分在 VM 上安装 SQL Server 和 mssql-tools。 在所有节点上执行这些操作。 有关详细信息，请参阅[在 Red Hat VM 上安装 SQL Server](/sql/linux/quickstart-install-connect-red-hat)。

### <a name="installing-sql-server-on-the-vms"></a>在 VM 上安装 SQL Server

使用以下命令安装 SQL Server：

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>打开防火墙端口 1433 以建立远程连接

需要在 VM 上打开端口 1433 才能建立远程连接。 使用以下命令在每个 VM 的防火墙中打开端口 1433：

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>安装 SQL Server 命令行工具

使用以下命令安装 SQL Server 命令行工具。 有关详细信息，请参阅[安装 SQL Server 命令行工具](/sql/linux/quickstart-install-connect-red-hat#tools)。

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> 为方便起见，请将 /opt/mssql-tools/bin/ 添加到 PATH 环境变量。 这样可以在不指定完整路径的情况下运行这些工具。 运行以下命令以修改登录会话和交互式/非登录会话的 PATH：</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>检查 SQL Server 的状态

完成配置后，可以检查 SQL Server 的状态，并验证它是否正在运行：

```bash
systemctl status mssql-server --no-pager
```

应该会看到以下输出：

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>配置 SQL Server Always On 可用性组

使用以下步骤为 VM 配置 SQL Server Always On 可用性组。 有关详细信息，请参阅[配置 SQL Server Always On 可用性组以在 Linux 上实现高可用性](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>启用 AlwaysOn 可用性组，并重启 mssql-server

在托管 SQL Server 实例的每个节点上启用 AlwaysOn 可用性组。 然后重启 mssql-server。 运行以下脚本：

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>创建证书

目前不支持对 AG 终结点进行 AD 身份验证。 因此，必须使用证书来加密 AG 终结点。

1. 使用 SQL Server Management Studio (SSMS) 或 SQL CMD 连接到**所有节点**。 运行以下命令，以启用 AlwaysOn_health 会话并创建主密钥：

    > [!IMPORTANT]
    > 如果远程连接到 SQL Server 实例，则需要在防火墙中打开端口 1433。 此外，需要在每个 VM 的 NSG 中允许与端口 1433 建立入站连接。 有关创建入站安全规则的详细信息，请参阅[创建安全规则](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)。

    - 请将 `<Master_Key_Password>` 替换为自己的密码。


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. 使用 SSMS 或 SQL CMD 连接到主要副本。 以下命令将在主要 SQL Server 副本上的 `/var/opt/mssql/data/dbm_certificate.cer` 中创建一个证书，并在 `var/opt/mssql/data/dbm_certificate.pvk` 中创建一个私钥：

    - 请将 `<Private_Key_Password>` 替换为自己的密码。

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

运行 `exit` 命令退出 SQL CMD 会话，并返回到 SSH 会话。
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>将证书复制到次要副本并在服务器上创建证书

1. 将创建的两个文件复制到所有要托管可用性副本的服务器上的同一位置。
 
    在主服务器上，运行以下 `scp` 命令将证书复制到目标服务器：

    - 请将 `<username>` 和 `<VM2>` 替换为所用的用户名和目标 VM 名称。
    - 针对所有次要副本运行此命令。

    > [!NOTE]
    > 无需运行提供根环境的 `sudo -i`。 只需在每个命令的前面运行 `sudo` 命令，就像我们在本教程中所做的那样。

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. 在目标服务器上运行以下命令：

    - 请将 `<username>` 替换为你的用户名。
    - `mv` 命令将文件或目录从一个位置移到另一个位置。
    - `chown` 命令用于更改文件、目录或链接的所有者和组。
    - 针对所有次要副本运行这些命令。

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. 以下 Transact-SQL 脚本基于在主要 SQL Server 副本上创建的备份创建证书。 使用强密码更新脚本。 解密密码与前一步骤中用于创建 .pvk 文件的密码相同。 若要创建证书，请在所有辅助服务器上使用 SQL CMD 或 SSMS 运行以下脚本：

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>在所有副本上创建数据库镜像终结点

使用 SQL CMD 或 SSMS 在所有 SQL 实例上运行以下脚本：

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>创建可用性组

使用 SQL CMD 或 SSMS 连接到托管主要副本的 SQL Server 实例。 运行以下命令以创建可用性组：

- 请将 `ag1` 替换为所需的可用性组名称。
- 请将 `<VM1>`、`<VM2>` 和 `<VM3>` 值替换为托管副本的 SQL Server 实例的名称。

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>为 Pacemaker 创建 SQL Server 登录名

在所有 SQL Server 上，为 Pacemaker 创建 SQL 登录名。 以下 Transact-SQL 创建登录名。

- 请将 `<password>` 替换为自己的复杂密码。

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

在所有 SQL Server 上，保存 SQL Server 登录名使用的凭据。 

1. 创建文件：

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. 将以下 2 行添加到该文件中：

    ```bash
    pacemakerLogin
    <password>
    ```

    若要退出 **vi** 编辑器，请先按 **Esc** 键，然后输入命令 `:wq` 以写入文件并退出。

1. 使该文件只能由 root 用户读取：

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>将次要副本加入可用性组

1. 若要将次要副本加入 AG，需要在所有服务器的防火墙中打开端口 5022。 在 SSH 会话中运行以下命令：

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. 在次要副本上，运行以下命令以将其加入 AG：

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. 在主要副本和每个次要副本上运行以下 Transact-SQL 脚本：

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. 加入次要副本后，在 SSMS 对象资源管理器中展开“Always On 高可用性”节点即可看到这些副本： 

    ![availability-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>将数据库添加到可用性组

我们将按照[配置可用性组一文来添加数据库](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)。

此步骤使用以下 Transact-SQL 命令。 在主要副本上运行以下命令：

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>验证是否已在辅助服务器上创建了数据库

在每个次要 SQL Server 副本上运行以下查询，查看 db1 数据库是否已创建并处于 SYNCHRONIZED（已同步）状态：

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

如果 `synchronization_state_desc` 将 `db1` 的状态列为 SYNCHRONIZED，则表示副本已同步。 次要副本在主要副本中显示 `db1`。

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>在 Pacemaker 群集中创建可用性组资源

我们将按照[在 Pacemaker 群集中创建可用性组资源](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)指南进行操作。

### <a name="create-the-ag-cluster-resource"></a>创建 AG 群集资源

1. 使用以下命令在可用组 `ag1` 中创建资源 `ag_cluster`。

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. 在继续使用以下命令之前，请检查资源并确保其处于联机状态：

    ```bash
    sudo pcs resource
    ```

    应该会看到以下输出：

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>创建虚拟 IP 资源

1. 使用网络中的可用静态 IP 地址创建虚拟 IP 资源。 可以使用命令工具 `nmap` 查找可用的 IP 地址。

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. 将 **stonith-enabled** 属性设置为 false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. 使用以下命令创建虚拟 IP 资源：

    - 请将以下 `<availableIP>` 值替换为未使用的 IP 地址。

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>添加约束

1. 要确保 IP 地址和 AG 资源在同一节点上运行，必须配置并置约束。 运行以下命令：

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. 创建排序约束以确保 AG 资源在 IP 地址之前启动并运行。 虽然并置约束意味着排序约束，但这将强制执行它。

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. 若要验证约束，请运行以下命令：

    ```bash
    sudo pcs constraint list --full
    ```

    应该会看到以下输出：

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>重新启用 stonith

我们现已准备好进行测试。 在节点 1 上运行以下命令，以在群集中重新启用 stonith：

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>检查群集状态

可以使用以下命令来检查群集资源的状态：

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>测试故障转移

为了确保到目前为止完成的配置能够成功，我们将测试故障转移。 有关详细信息，请参阅 [Linux 上的 Always On 可用性组故障转移](/sql/linux/sql-server-linux-availability-group-failover-ha)。

1. 运行以下命令，将主要副本手动故障转移到 `<VM2>`。 请将 `<VM2>` 替换为你的服务器名称值。

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. 如果再次检查约束，将会看到由于手动故障转移而添加了另一个约束：

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. 使用以下命令删除 ID 为 `cli-prefer-ag_cluster-master` 的约束：

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. 使用命令 `sudo pcs resource` 检查群集资源，应会看到主要实例现已变为 `<VM2>`。

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>测试隔离

可以运行以下命令来测试 STONITH。 尝试从 `<VM1>` 对 `<VM3>` 运行以下命令。

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> 默认情况下，隔离操作会关闭节点，然后将其打开。 如果只想使节点脱机，请在命令中使用选项 `--off`。

应会收到以下输出：

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
有关测试隔离设备的详细信息，请参阅以下 [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) 文章。

## <a name="next-steps"></a>后续步骤

若要对 Azure 中创建的 SQL Server 使用可用性组侦听器，首先需要创建并配置负载均衡器。

> [!div class="nextstepaction"]
> [在 Azure 门户中创建并配置负载均衡器](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)