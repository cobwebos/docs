---
title: 连接到启用了 Azure Arc 的 SQL 托管实例
description: 连接到启用了 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934353"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>连接到启用了 Azure Arc 的 SQL 托管实例

本文介绍了如何连接到已启用 Azure Arc 的 SQL 托管实例。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>查看启用了 Azure Arc 的 SQL 托管实例

若要查看启用了 Azure Arc 的 SQL 托管实例和外部终结点，请使用以下命令：

```console
azdata arc sql mi list
```

输出应如下所示：

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

如果你使用的是 AKS、kubeadm 或 OpenShift 等，则可以从此处复制外部 IP 和端口号，并使用你喜欢的工具连接到该服务器，以便连接到 SQL server/Azure SQL 实例，如 Azure Data Studio 或 SQL Server Management Studio。  但是，如果使用的是快速入门 VM，请参阅下面的有关如何从 Azure 外部连接到该 VM 的特殊信息。 

> [!NOTE]
> 你的公司策略可能会阻止访问 IP 和端口，尤其是在公有云中创建的情况下。

## <a name="connect"></a>连接 

用 Azure Data Studio、SQL Server Management Studio 或 SQLCMD 进行连接

打开 Azure Data Studio，然后连接到具有上述外部终结点 IP 地址和端口号的实例。 如果你使用的是 Azure VM，则需要 _公共_ IP 地址，该地址可使用 [有关 Azure 虚拟机部署的特别注意事项](#special-note-about-azure-virtual-machine-deployments)来识别。

例如：

- 服务器：52.229.9.30、30913
- 用户名： sa
- 密码：预配时指定的 SQL 密码

> [!NOTE]
> 您可以使用 Azure Data Studio [查看 SQL 托管实例仪表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)。

若要使用 SQLCMD 或 Linux 或 Windows 进行连接，可以使用如下所示的命令。 在出现提示时输入 SQL 密码：

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>有关 Azure 虚拟机部署的特别注意事项

如果你使用的是 Azure 虚拟机，则终结点 IP 地址将不显示公共 IP 地址。 若要查找外部 IP 地址，请使用以下命令：

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然后，可以将公共 IP 地址与端口组合在一起，以进行连接。

你可能还需要通过网络安全网关 (NSG) 来公开 sql 实例的端口。 若要允许流量通过 (NSG) ，你需要添加一个规则，你可以使用以下命令执行此操作。

若要设置规则，你将需要知道 NSG 的名称，你可以使用以下命令来找出此名称：

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

获得 NSG 的名称后，可以使用以下命令添加防火墙规则。 此处的示例值为端口30913创建 NSG 规则，并允许来自 **任何** 源 IP 地址的连接。  这不是最佳安全做法！  通过指定特定于你的客户端 IP 地址或 IP 地址范围（涵盖你的团队或组织的 IP 地址）的 -source-address-prefixes 值，可以更好地锁定内容。

将下面参数的值替换 `--destination-port-ranges` 为你在上面的 F 命令中获取的端口号 `azdata sql instance list` 。

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>后续步骤

- [查看 SQL 托管实例仪表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [查看 Azure 门户中的 SQL 托管实例](view-arc-data-services-inventory-in-azure-portal.md)
