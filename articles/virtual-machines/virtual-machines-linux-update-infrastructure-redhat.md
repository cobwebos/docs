<!-- not suitable for Mooncake -->

<properties
   pageTitle="Red Hat 更新基础结构 (RHUI) | Azure"
   description="了解用于 Azure 中按需 Red Hat Enterprise Linux 实例的 Red Hat 更新基础结构 (RHUI)"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
	ms.service="virtual-machines-linux"
	ms.date="07/11/2016"
	wacn.date=""/>

# 用于 Azure 中按需 Red Hat Enterprise Linux VM 的 Red Hat 更新基础结构 (RHUI)

从 Azure 应用商店中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的虚拟机已注册，以访问 Azure 中部署的 Red Hat 更新基础结构 (RHUI)。按需 RHEL 实例将可以访问到区域 yum 存储库并能够接收增量更新。

受 RHUI 管理的 yum 存储库列表是预配时在 RHEL 实例中配置的。你无需进行任何额外的配置，只需在运行 RHEL 实例后运行 `yum update` 即可获取最新更新。

## RHUI 概述
[Red Hat 更新基础结构](https://access.redhat.com/products/red-hat-update-infrastructure) 提供了一种高度可伸缩的解决方案，用于为 Red Hat 认证的云提供商所托管的 Red Hat Enterprise Linux 云实例管理 yum 存储库内容。在上游 Pulp 项目的基础上，云提供商可以通过 RHUI 在本地镜像 Red Hat 所托管的存储库内容、使用自己的内容创建自定义存储库，以及使用经过负载平衡的内容传送系统将这些存储库提供给庞大的最终用户群。

## 提供 RHUI 的区域
在 RHEL 按需映像可用的所有区域中都提供了 RHUI。它当前包括 [Azure 状态仪表板](https://azure.microsoft.com/status/)页上列出的所有公共区域。从 RHEL 按需映像预配的 VM 的 RHUI 访问权限包含在其价格中。当我们将来扩展 RHEL 按需可用性时，将更新区域/国家云可用性。

> [AZURE.NOTE] 对 Azure 托管的 RHUI 的访问将限制为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内的 VM。

## 从另一个更新存储库获取更新

如果你需要从其他更新存储库（而不是 Azure 托管的 RHUI）获取更新，则需要从 RHUI 取消注册实例，然后使用所需的更新基础结构（如 Red Hat Satellite 或 Red Hat 客户门户 CDN）重新注册它们。你需要这些服务的相应 Red Hat 订阅，并需要注册 [Azure 中的 Red Hat 云访问](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)。

若要取消注册 RHUI 并重新注册到你的更新基础结构，请执行以下步骤。

1.	编辑 /etc/yum.repos.d/rh-cloud.repo，将所有 `enabled=1` 更改为 `enabled=0`。例如：

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	编辑 /etc/yum/pluginconf.d/rhnplugin.conf，将 `enabled=0` 更改为 `enabled=1`。
3.	然后注册到所需的基础结构，如 Red Hat 客户门户。请按照[如何在 Red Hat 客户门户中注册和订阅系统](https://access.redhat.com/solutions/253273)中的 Red Hat 解决方案指南进行操作。

> [AZURE.NOTE] 对 Azure 托管的 RHUI 的访问权限包含在 RHEL 即用即付 (PAYG) 映像价格中。从 Azure 托管的 RHUI 中取消注册 PAYG RHEL VM 不会将虚拟机转换为自带许可 (BYOL) 类型的 VM，因此如果你将同一个 VM 注册到另一个更新源，可能会产生双倍费用。<p> 如果始终需要使用 Azure 托管的 RHUI 以外的更新基础结构，请考虑创建和部署你自己的（BYOL 类型）映像，如 [Create and Upload Red Hat-based virtual machine for Azure](/documentation/articles/virtual-machines-linux-redhat-create-upload-vhd/)（为 Azure 创建和上载基于 Red Hat 的虚拟机）一文中所述。

## 后续步骤
若要从 Azure 应用商店的即用即付映像创建 Red Hat Enterprise Linux VM 并利用 Azure 托管的 RHUI，请转到 [Azure 应用商店](https://azure.microsoft.com/marketplace/partners/redhat/)。你将能够在 RHEL 实例中使用 `yum update`，而无需任何其他设置。

<!---HONumber=Mooncake_0808_2016-->