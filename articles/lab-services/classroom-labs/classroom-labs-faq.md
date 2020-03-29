---
title: Azure 实验室服务中的课堂实验室 » 常见问题解答 |微软文档
description: 本文提供了有关 Azure 实验室服务中教室实验室的常见问题 （FAQ） 的解答。
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 8d1ed128181d036af0026ae273c2c5bf1d3a066e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443493"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure 实验室服务中的课堂实验室 • 常见问题 （FAQ）
获取有关 Azure 实验室服务中教室实验室的一些最常见问题的解答。 

## <a name="quotas"></a>配额

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>每个用户、每周或整个实验室的持续时间的配额？ 
为实验室设置的配额适用于整个实验的整个期间每个学生。 而且[，VM 的计划运行时间](how-to-create-schedules.md)不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。  有关配额的详细信息，请参阅[为用户设置配额](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="if-professor-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>如果教授打开学生 VM，这会影响学生配额吗？ 
不是。 不是的。 当教授打开学生 VM 时，它不会影响分配给学生的配额。 

## <a name="schedules"></a>计划

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>设置计划时，实验室中的所有 VM 是否自动启动？ 
不是。 不是所有的 VM。 仅按计划分配给用户的 VM。 未分配给用户的 VM 不会自动启动。 是设计设计的 

## <a name="lab-accounts"></a>实验室帐户

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>为什么由于地址范围不可用而无法创建实验室？ 
课堂实验室可以在您在 Azure 门户中创建实验室帐户时指定的 IP 地址范围内创建实验室 VM。 提供地址范围时，为实验室 VM 分配了 512 个 IP 地址后创建的每个实验室。 实验室帐户的地址范围必须足够大，以容纳您打算在实验室帐户下创建的所有实验室。 

例如，如果您有一个块 /19 - 10.0.0.0/19，此地址范围可容纳 8192 个 IP 地址和 16 个实验室（8192/512 = 16 个实验室）。 在这种情况下，实验室创建在 17 日实验室创建时失败。

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应该在组织的防火墙设置上打开哪些端口范围，以便通过 RDP/SSH 连接到实验室虚拟机？

端口为：49152*65535。 教室实验室在负载均衡器后面。 每个实验室都有一个公共 IP 地址，实验室中的每个虚拟机都有一个唯一的端口。 

您还可以在 Azure 门户中实验室的主页的**虚拟机池**选项卡上查看每个虚拟机的专用 IP 地址。 如果重新发布实验室，实验室的公共 IP 地址将不会更改，但实验室中每个虚拟机的专用 IP 和端口号可能会更改。 您可以在本文中了解详细信息[：Azure 实验室服务的防火墙设置](how-to-configure-firewall-settings.md)。

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>我应该在组织的防火墙设置上打开哪些公共 IP 地址范围，以便通过 RDP/SSH 连接到实验室虚拟机？
请参阅[Azure IP 范围和服务标记 – 公共云](https://www.microsoft.com/download/details.aspx?id=56519)，它为 Azure 中的数据中心提供公共 IP 地址范围。 您可以打开实验室帐户所在的区域的 IP 地址。

## <a name="virtual-machine-images"></a>虚拟机映像

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>作为实验室创建者，为什么在创建新实验室时，我不能在虚拟机映像下拉列表中启用其他映像选项？

当管理员将您作为实验室创建者添加到实验室帐户时，您被授予创建实验室的权限。 但是，您无权编辑实验室帐户中的任何设置，包括启用的虚拟机映像的列表。 要启用其他映像，请与实验室帐户管理员联系，以便为您执行此操作，或要求管理员将您作为参与者角色添加到实验室帐户。 "参与者"角色将授予您在实验室帐户中编辑虚拟机映像列表的权限。

## <a name="users"></a>用户

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>有多少用户可以在教室实验室？
您最多可以将 400 个用户添加到教室实验室。 

## <a name="blog-post"></a>博客文章
订阅[Azure 实验室服务博客](https://azure.microsoft.com/blog/tag/azure-lab-services/)。

## <a name="update-notifications"></a>更新通知
订阅[实验室服务更新](https://azure.microsoft.com/updates/?product=lab-services)，随时了解实验室服务中的新功能。

## <a name="general"></a>常规
### <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？
如果您的问题未在此处列出，请告诉我们，以便我们可以帮助您找到答案。

- 在本常见问题解答的末尾发布问题。 
- 要覆盖更广泛的受众，在[Azure 实验室服务 - 堆栈溢出论坛](https://stackoverflow.com/questions/tagged/azure-lab-services)上发布问题。 
- 对于功能请求，请将请求和想法提交给[Azure 实验室服务 - 用户语音](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)。

