---
title: Azure 实验室服务中的课堂实验室 - FAQ | Microsoft Docs
description: 本文提供有关 Azure 实验室服务中课堂实验室的常见问题解答 (FAQ)。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: 11cb0d995537b8979c3727bf508045c6e31fc208
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592604"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 实验室服务中的课堂实验室 - 常见问题解答 (FAQ)
获取关于 Azure 实验室服务中课堂实验室的某些最常见问题的解答。 

## <a name="quotas"></a>配额

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>是每个用户或每周配额，还是每整个实验室期间的配额？ 
为实验室设置的配额针对整个实验室期间的每个学生。 而且，[VM 的计划内运行时间](how-to-create-schedules.md)不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。  有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>如果教师启用了学生 VM，会影响学生配额吗？ 
不是。 不会。 当教师启用学生 VM 时，它不会影响分配给该学生的配额。 

## <a name="schedules"></a>计划

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>设置计划后，实验室中的所有 VM 是否自动启动？ 
不是。 并非所有 VM。 仅按计划分配给用户的 VM。 未分配给用户的 VM 不会自动启动。 这是由设计决定的。 

## <a name="lab-accounts"></a>实验室帐户

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>为什么我无法创建实验室，是因为地址范围不可用吗？ 
在 Azure 门户中创建实验室帐户时，课堂实验室可以在指定的 IP 地址范围内创建实验室 VM。 提供地址范围时，为实验室 VM 分配 512 IP 地址后将创建各个实验室。 实验室帐户的地址范围必须足够大，以容纳要在实验室帐户下创建的所有实验室。 

例如，如果你的区块为 /19 - 10.0.0.0/19，则此地址范围可容纳 8192 个 IP 地址和 16 个实验室（8192/512 = 16 个实验室）。 在这种情况下，实验室创建会在创建第 17 个实验室时失败。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应在组织的防火墙设置上打开哪些端口范围才能通过 RDP/SSH 连接到实验室虚拟机？

端口如下：49152–65535。 课堂实验室位于负载均衡器后面。 每个实验室都有一个公共 IP 地址，并且实验室中的每个虚拟机都有一个唯一的端口。 

还可以在 Azure 门户中“实验室”主页的“虚拟机池”选项卡上查看每个虚拟机的专用 IP 地址。 如果重新发布实验室，实验室的公共 IP 地址不会更改，但实验室中每个虚拟机的专用 IP 和端口号可以更改。 可以在以下文章中了解详细信息：[Azure 实验室服务的防火墙设置](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应在组织的防火墙设置上打开哪些公共 IP 地址范围才能通过 RDP/SSH 连接到实验室虚拟机？
请参阅 [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)，它为 Azure 中的数据中心提供公共 IP 地址范围。 可以打开实验室帐户所在区域的 IP 地址。

## <a name="virtual-machine-images"></a>虚拟机映像

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>作为实验室创建者，为什么在创建新实验室时无法在虚拟机映像下拉列表中启用其他映像选项？

当管理员将你作为实验室创建者添加到实验室帐户时，你将获得创建实验室的权限。 但是，你没有权限编辑实验室帐户中的任何设置，包括已启用的虚拟机映像列表。 若要启用其他映像，请联系实验室帐户管理员为你执行此操作，或者要求管理员将你作为参与者角色添加到实验室帐户。 参与者角色将授予你编辑实验室帐户中的虚拟机映像列表的权限。

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>是否可以将更多的磁盘附加到虚拟机？
不是。 不能将其他磁盘附加到课堂实验室中的 VM。 

## <a name="users"></a>用户

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>课堂实验室中可以有多少用户？
最多可以向一个课堂实验室添加 400 个用户。 

## <a name="blog-post"></a>博客文章
订阅 [Azure 实验室服务博客](https://aka.ms/azlabs-blog)。

## <a name="update-notifications"></a>更新通知
订阅[实验室服务更新](https://azure.microsoft.com/updates/?product=lab-services)，随时了解实验室服务中的新增功能。

## <a name="general"></a>常规
### <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？
如果此处未列出问题，请联系我们寻求答案。

- 在本常见问题解答的末尾发布问题。 
- 如果希望更多人看到问题，可以将问题发布在 [Azure 实验室服务 - 技术社区论坛](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)上。 
- 要提出功能请求，请将请求和想法提交到 [Azure 实验室服务 - 用户之声](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

