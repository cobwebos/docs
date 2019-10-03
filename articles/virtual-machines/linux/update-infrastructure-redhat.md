---
title: Red Hat 更新基础结构 | Microsoft Docs
description: 了解用于 Microsoft Azure 中按需 Red Hat Enterprise Linux 实例的 Red Hat 更新基础结构
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 6b332af53f421230b3fb5401e525bd77c5e87ed9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081385"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构
 [Red Hat 更新基础结构](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) 允许云提供程序（如 Azure）镜像 Red Hat 托管的存储库内容，创建包含 Azure 特定内容的自定义存储库，并将其提供给最终用户 VM 使用。

已预先配置 Red Hat Enterprise Linux (RHEL) 即用即付 (PAYG) 映像来访问 Azure RHUI。 不需要任何其他配置。 要获取最新更新，请在 RHEL 实例准备好后运行 `sudo yum update`。 RHEL PAYG 软件费涵盖了此服务。

[此处](./rhel-images.md)提供了关于 Azure 中的 RHEL 映像的更多信息，包括发布和保留策略。

可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。

## <a name="important-information-about-azure-rhui"></a>有关 Azure RHUI 的重要信息

* Azure RHUI 是支持在 Azure 中创建的所有 RHEL PAYG Vm 的更新基础结构。 这不会阻止你向订阅管理器或附属或其他更新源注册 PAYG RHEL Vm, 但使用 PAYG VM 执行此操作将导致间接双重计费。 有关详细信息, 请参阅以下要点。
* RHEL PAYG 映像价格涵盖了 Azure 托管 RHUI 的访问权限。 从 Azure 托管的 RHUI 注销 PAYG RHEL VM 不会将虚拟机转换为自带许可 (BYOL) 类型的 VM。 如果向另一更新源注册同一 VM，可能会产生间接双倍费用。 第一次你需要支付 Azure RHEL 软件费。 第二次你需要支付之前已购买的 Red Hat 订阅费。 如果你始终需要使用 Azure 托管的 RHUI 以外的更新基础结构, 请考虑注册使用[RHEL BYOS 映像](https://aka.ms/rhel-byos)。

* Azure 中的 RHEL SAP PAYG 映像（RHEL for SAP、RHEL for SAP HANA 以及 RHEL for SAP Business Application）已根据 SAP 认证需要，连接到保留在特定 RHEL 次要版本上的专用 RHUI 通道。

* 对 Azure 托管的 RHUI 的访问限制为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。 若要通过本地网络基础结构代理所有 VM 流量，可能需要为 RHEL PAYG VM 设置用户定义的路由才能访问 Azure RHUI。 如果是这种情况, 则需要为_所有_RHUI IP 地址添加用户定义的路由。

## <a name="image-update-behavior"></a>图像更新行为

自2019年4月起, Azure 提供 RHEL 映像, 默认情况下连接到扩展更新支持 (EUS) 存储库, 默认情况下连接到常规 (非 EUS) 存储库的 RHEL 映像。 有关 RHEL EUS 的更多详细信息, 请参阅 Red Hat 的[版本生命周期文档](https://access.redhat.com/support/policy/updates/errata)和[EUS 文档](https://access.redhat.com/articles/rhel-eus)。 的`sudo yum update`默认行为取决于从预配的 RHEL 映像, 因为不同的映像连接到不同的存储库。

有关完整映像列表, 请使用`az vm image list --publisher redhat --all` Azure CLI 运行。

### <a name="images-connected-to-non-eus-repositories"></a>连接到非 EUS 存储库的映像

如果从连接到非 EUS 存储库的 RHEL 映像预配 VM, 则在运行`sudo yum update`时, 会升级到最新的 RHEL 次要版本。 例如, 如果从 rhel 7.4 PAYG 映像预配 VM 并运行`sudo yum update`, 最终会获得 rhel 7.7 VM (RHEL7 系列中的最新次要版本)。

连接到 EUS 存储库的映像不会在 SKU 中包含次版本号。 SKU 是 URN 中的第三个元素 (映像的完整名称)。 例如, 以下所有映像均附加到非 EUS 存储库:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

请注意, Sku 为 7-LVM 或 7-RAW。 在这些映像的版本 (URN 中的第四个元素) 中指出了次版本。

### <a name="images-connected-to-eus-repositories"></a>连接到 EUS 存储库的映像

如果从连接到 EUS 存储库的 RHEL 映像预配 VM, 则在运行`sudo yum update`时将不会升级到最新的 RHEL 次要版本。 这是因为连接到 EUS 存储库的图像也被版本锁定为特定的次要版本。

连接到 EUS 存储库的映像将在 SKU 中包含次版本号。 例如, 以下所有映像均附加到 EUS 存储库:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS 和版本锁定 RHEL VM

一些客户可能希望在预配 VM 后将其 RHEL Vm 锁定到某个 RHEL 次要版本。 可以通过对存储库进行更新以指向扩展的更新支持存储库来将 RHEL VM 的版本锁定到特定的次版本。 还可以撤消 EUS 版本锁定操作。

>[!NOTE]
> RHEL 附加上不支持 EUS。 这意味着, 如果你要安装的包通常在 RHEL 额外通道中可用, 则在 EUS 上将无法执行此操作。 [此处](https://access.redhat.com/support/policy/updates/extras/)详细介绍了 Red Hat 额外产品生命周期。

撰写本文时, RHEL < = 7.3 的 EUS 支持已结束。 有关更多详细信息, 请参阅[Red Hat 文档](https://access.redhat.com/support/policy/updates/errata/)中的 "Red Hat Enterprise Linux 更长的支持外接程序" 部分。
* RHEL 7.4 EUS 支持于8月 2019 31 日结束
* RHEL 7.5 EUS 支持于年4月 30 2020 日结束
* RHEL 7.6 EUS 支持于10月31日结束, 2020
* RHEL 7.7 EUS 支持于8月30日结束, 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>将 RHEL VM 切换到 EUS (版本-锁定到特定的次版本)
使用以下说明将 RHEL VM 锁定到特定次要版本 (以 root 身份运行):

>[!NOTE]
> 这仅适用于 EUS 可用的 RHEL 版本。 撰写本文时, 这包括 RHEL 7.2-7.7。 有关更多详细信息，请访问 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)页。

1. 禁用非 EUS 存储库：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. 添加 EUS 存储库：
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. 锁定 releasever 变量 (以 root 身份运行):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 以上指令会将 RHEL 次版本锁定到当前次版本。 如果希望进行升级并锁定到不是最新版本的较高次版本，请输入具体的次版本。 例如，`echo 7.5 > /etc/yum/vars/releasever` 会将 RHEL 版本锁定到 RHEL 7.5

1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>将 RHEL VM 切换回非 EUS (删除版本锁)
以 root 身份运行以下内容:
1. 删除 releasever 文件:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 禁用 EUS 存储库:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. 配置 RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. 更新 RHEL VM
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 内容传送服务器的 IP

在 RHEL 按需映像可用的所有区域中都提供了 RHUI。 目前包括 [Azure 状态仪表板](https://azure.microsoft.com/status/)页上列出的所有公共区域、Azure 美国政府区域和 Microsoft Azure 德国区域。

如果使用网络配置进一步限制来自 RHEL PAYG VM 的访问，根据所处环境，请确保允许使用以下 IP 以便 `yum update` 能够正常工作：


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 基础结构


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>更新 VM 上已过期的 RHUI 客户端证书

如果使用较旧的 RHEL VM 映像（例如，RHEL 7.4（映像 URN：`RedHat:RHEL:7.4:7.4.2018010506`）），则由于 SSL 客户端证书现已过期，你将遇到与 RHUI 的连接问题。 你看到的错误可能类似于“SSL 对等机已拒绝你的证书，因为已过期”或“错误：无法为存储库检索存储库元数据(repom .xml): ...请验证其路径并重试”_ 。 若要解决此问题，请使用以下命令更新 VM 上的 RHUI 客户端程序包：

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

另外，运行 `sudo yum update` 也可能会更新客户端证书包（具体取决于 RHEL 版本），尽管你将看到其他存储库的“SSL 证书已过期”错误。 如果此更新成功，则应当还原与其他 RHUI 存储库的正常连接，以便能够成功运行 `sudo yum update`。

如果在运行`yum update`时遇到404错误, 请尝试以下操作来刷新 yum 缓存:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>排查 Azure RHUI 连接问题
如果从 Azure RHEL PAYG VM 连接到 Azure RHUI 时遇到问题，请按照下列步骤操作：

1. 检查 Azure RHUI 终结点的 VM 配置：

    1. 检查 `/etc/yum.repos.d/rh-cloud.repo` 文件 `[rhui-microsoft-azure-rhel*]` 部分的 `baseurl` 是否包含对 `rhui-[1-3].microsoft.com` 的引用。 如果是，则使用的是新 Azure RHUI。

    1. 如果它指向 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位置，则需要更新配置。 你使用的是旧 VM 快照，需要将其更新为指向新的 Azure RHUI。

1. Azure 托管的 RHUI 仅限 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM 进行访问。

1. 如果使用新配置并已确认 VM 从 Azure IP 范围建立了连接，但仍无法连接到 Azure RHUI，请向 Microsoft 或 Red Hat 提出支持案例。

### <a name="infrastructure-update"></a>基础结构更新

2016 年 9 月，我们部署了更新的 Azure RHUI。 2017 年 4 月，我们关闭了旧版 Azure RHUI。 如果一直在使用 2016 年 9 月或之后的 RHEL PAYG 映像（或其快照），则会自动连接到新的 Azure RHUI。 但是，如果在 VM 上使用旧版本的快照，则需手动更新其配置以访问 Azure RHUI，如以下部分所述。

新的 Azure RHUI 服务器通过 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)进行配置。 在流量管理器中，任何 VM 都可使用单一终结点 (rhui-1.microsoft.com)，而无需考虑区域。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>执行手动更新过程以使用 Azure RHUI 服务器
此过程仅供参考。 RHEL PAYG 映像已包含用于连接 Azure RHUI 的正确配置。 要手动更新配置以使用 Azure RHUI 服务器，请完成以下步骤：

- 适用于 RHEL 6：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- 适用于 RHEL 7：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>后续步骤
* 要通过 Azure 市场 PAYG 映像创建 Red Hat Enterprise Linux VM 并利用 Azure 托管的 RHUI，请转到 [Azure 市场](https://azure.microsoft.com/marketplace/partners/redhat/)。
* 若要详细了解 Azure 中的 Red Hat 映像，请转到此[文档页面](./rhel-images.md)。
* 可以在 [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata)（Red Hat Enterprise Linux 生命周期）页找到有关 RHEL 所有版本的 Red Hat 支持策略的信息。
