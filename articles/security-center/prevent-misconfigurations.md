---
title: 如何防止 Azure 安全中心配置错误
description: 了解如何使用安全中心的 "强制" 和 "拒绝" 选项来了解建议详细信息页
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: ceafbe1fd1682fc5e92ab3048ed09866fb9ebbdf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570423"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>防止通过强制/拒绝建议配置错误

安全错误配置是安全事件的主要原因。 安全中心现在能够帮助 *防止* 新资源的配置错误，这与特定建议相关。 

此功能可帮助保护工作负荷的安全并使安全分数稳定。

根据特定建议强制实施安全配置，以两种模式提供：

- 使用 Azure 策略的 **拒绝** 效果，可阻止创建不正常的资源

- 使用 **强制** 选项，你可以利用 Azure 策略的 **DeployIfNotExist** 效果，并在创建时自动修正不合规的资源
 
这适用于所选的安全建议，可在资源详细信息页的顶部找到。

## <a name="prevent-resource-creation"></a>阻止资源创建

1. 打开新资源必须满足的建议，然后选择页面顶部的 " **拒绝** " 按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="突出显示 拒绝 按钮的建议页面":::

    此时将打开 "配置" 窗格，其中列出了作用域选项。 

1. 通过选择相关的订阅或管理组来设置作用域。

    > [!TIP]
    > 您可以使用该行末尾的三个点来更改单个订阅，或使用复选框来选择多个订阅或组，然后选择 " **更改为拒绝**"。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="设置 Azure 策略拒绝的作用域":::


## <a name="enforce-a-secure-configuration"></a>强制实施安全配置

1. 如果新资源不满足此建议，请打开要为其部署模板部署的建议，然后选择页面顶部的 " **强制执行** " 按钮。

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="突出显示了 强制 按钮的建议页":::

    此时将打开 "配置" 窗格，其中包含所有策略配置选项。 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="强制配置选项":::

1. 设置 "作用域"、"分配名称" 和其他相关选项。

1. 选择“查看 + 创建”。

## <a name="recommendations-with-denyenforce-options"></a>带有 deny/强制选项的建议

这些建议可与 **deny** 选项一起使用：

- 应将虚拟机迁移到新的 Azure 资源管理器资源
- 应将存储帐户迁移到新 Azure 资源管理器资源
- 从事件中心命名空间删除 RootManageSharedAccessKey 之外的所有授权规则
- 应从服务总线命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则
- 应该启用安全传输到存储帐户
- 应该启用只能通过安全方式连接到 Redis 缓存
- 自动化帐户变量应进行加密
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 审核对存储帐户的不受限的网络访问


这些建议可与 " **强制** " 选项一起使用：

- 应启用逻辑应用的诊断日志
- 应启用 Data Lake Analytics 中的诊断日志
- 应启用 IoT 中心的诊断日志
- 应启用 Batch 帐户中的诊断日志
- 应该在 Azure 流分析中启用诊断日志
- 应启用服务总线中的诊断日志
- 应启用搜索服务的诊断日志
- 应启用事件中心内的诊断日志
- 应启用虚拟机规模集中的诊断日志
- 应启用 Key Vault 中的诊断日志
- 应启用 SQL 服务器上的审核
- 应在 SQL 服务器上启用高级数据安全性



