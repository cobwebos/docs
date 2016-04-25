<!-- not suitable for Mooncake -->

<properties
   pageTitle="针对 Red Hat Enterprise Linux 映像的更新基础结构 | Azure"
   description="介绍 Azure 中按需 Red Hat Enterprise Linux 实例的 yum 更新服务"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
	ms.service="virtual-machines"
	ms.date="03/18/2016"
	wacn.date=""/>

# 针对 Red Hat Enterprise Linux 映像的更新基础结构

在 Azure 中使用 Red Hat Update Infrastructure (RHUI) 可以管理 Azure Red Hat Enterprise Linux (RHEL) 映像的 yum 存储库内容。然后即可使用从 Azure 库创建的按需 RHEL 实例来访问区域性的 yum 存储库。这样一来，RHEL 实例就可以接收增量更新。

受 RHUI 管理的 yum 存储库列表是预配时在 RHEL 实例中配置的。因此，你无需进行任何额外的配置，只需在运行 RHEL 实例后运行 `yum update` 即可。

## RHUI 概述
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) 提供了一种高度可伸缩的解决方案，用于为 Red Hat 认证的云提供商所托管的 Red Hat Enterprise Linux 云实例管理 yum 存储库内容。在上游 Pulp 项目的基础上，云提供商可以通过 RHUI 在本地镜像 Red Hat 所托管的存储库内容、使用自己的内容创建自定义存储库，以及使用经过负载平衡的内容传送系统将这些存储库提供给庞大的最终用户群。

## 提供 RHUI 的区域
[Azure 状态仪表板](https://azure.microsoft.com/status/)上列出的所有公共区域均提供 RHUI。这意味着，你可以获取这些区域的 yum 更新服务而无需支付任何额外的费用。此信息将在以后更新。

## 获取其他更新存储库（例如 Red Hat Network Satellite）提供的更新

若要获取其他更新存储库提供的更新，你需要有 Red Hat 云访问许可证，以及针对 Azure 的现成的 Red Hat 订阅。

然后，你需要先注销 RHUI，再注册到更新基础结构。以下是详细步骤。

1.	编辑 /etc/yum.repos.d/rh-cloud.repo，将所有 `enabled=1` 更改为 `enabled=0`。例如：

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	编辑 /etc/yum/pluginconf.d/rhnplugin.conf，将 `enabled=0` 更改为 `enabled=1`。
3.	然后，注册到 Red Hat Network (RHN)。

        rhn_register

    或

        rhnreg_ks


> [AZURE.NOTE] 将对出站数据传输收费（参见[定价详细信息](/home/features/data-transfers/#price)）。建议使用默认 RHUI 获取增量更新，这样就不需额外付费。

## 后续步骤
现在你已了解 Azure 中的 RHUI，因此可以从 [Azure 库](https://azure.microsoft.com/marketplace/partners/redhat/)创建 RHEL 映像并在 RHEL 实例中使用 `yum update`。

<!---HONumber=Mooncake_0418_2016-->