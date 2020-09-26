---
title: 将 GCP 帐户连接到 Azure 安全中心
description: 监视 Azure 安全中心的 GCP 资源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c4020bc5e96ff35fa2d04a872216e43abbba5323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328303"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>将 GCP 帐户连接到 Azure 安全中心

云工作负荷通常跨多个云平台，云安全服务必须执行相同的操作。

Azure 安全中心保护 Azure 中的工作负荷，Amazon Web Services (AWS) ，并 Google Cloud Platform (GCP) 。

将你的 GCP 帐户加入安全中心，将 GCP Security 命令和 Azure 安全中心集成在一起。 因此，安全中心可在这两个云环境之间提供可见性和保护，以提供：

- 检测安全配置错误
- 显示安全中心建议和 GCP 安全命令中心发现的单一视图
- 将 GCP 资源合并到安全中心的安全分数计算中
- 将基于 CIS 标准的 GCP 安全命令中心建议集成到安全中心的规章符合性仪表板中

在下面的屏幕截图中，可以看到在安全中心的 "概述" 仪表板中显示的 GCP 项目。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3在安全中心的 "概述" 仪表板上列出的 GCP 项目" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|需要 [用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|所需角色和权限：|相关 Azure 订阅的**所有者**或**参与者**|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="connect-your-gcp-account"></a>连接到你的 GCP 帐户

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>步骤 1。 设置具有安全运行状况分析的 GCP 安全命令中心

对于组织中的所有 GCP 项目，还必须执行以下操作：

1. 使用[GCP 文档中的说明](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)设置**GCP Security Command Center** 。
1. 使用[GCP 文档中的这些说明](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)启用**安全运行状况分析**。
1. 验证是否存在流向安全命令中心的数据。

有关为安全配置连接 GCP 环境的说明，请遵循 Google 的建议，以使用安全配置建议。 此集成利用 Google 安全命令中心，并将使用可能会影响计费的其他资源。

首次启用安全运行状况分析时，可能需要几个小时才能获得数据。


### <a name="step-2-enable-gcp-security-command-center-api"></a>步骤 2. 启用 GCP 安全命令中心 API

1. 从 Google 的 **云控制台 API 库**中，选择要连接到 Azure 安全中心的项目。
1. 在 API 库中，找到并选择 " **安全命令中心 API**"。
1. 在 API 的页上，选择 " **启用**"。

了解有关 [安全命令中心 API](https://cloud.google.com/security-command-center/docs/reference/rest/)的详细信息。


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>步骤 3. 为安全配置集成创建专用服务帐户

1. 在 **GCP 控制台**中，选择要连接到安全中心的项目。
1. 在 **导航菜单**中的 " **IAM & 管理** 选项" 下，选择 " **服务帐户**"。
1. 选择 " **创建服务帐户**"。
1. 输入帐户名称，并选择 " **创建**"。
1. 将 **角色** 指定为 **安全中心管理员查看器**，并选择 " **继续**"。
1. " **授予用户访问此服务帐户** " 部分是可选的。 选择“完成”。
1. 复制所创建服务帐户的 " **电子邮件" 值** ，并将其保存供以后使用。
1. 在**导航菜单**中的 " **iam & 管理**选项" 下，选择 " **iam** "
    1. 切换到组织级别。
    1. 选择 " **添加**"。
    1. 在 " **新成员** " 字段中，粘贴前面复制的 **电子邮件值** 。
    1. 将角色指定为 **安全中心管理查看器** ，然后选择 "保存"。
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="设置相关的 GCP 权限":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>步骤 4. 为专用服务帐户创建私钥
1. 切换到项目级别。
1. 在 **导航菜单**中的 " **IAM & 管理** 选项" 下，选择 " **服务帐户**"。
1. 打开专用服务帐户，然后选择 "编辑"。
1. 在 " **密钥** " 部分中，选择 " **添加密钥** "，然后选择 " **创建新密钥**"。
1. 在 "创建私钥" 屏幕中，选择 " **JSON**"，然后选择 " **创建**"。
1. 保存此 JSON 文件以供以后使用。


### <a name="step-5-connect-gcp-to-security-center"></a>步骤 5。 将 GCP 连接到安全中心 
1. 从安全中心的菜单中，选择 " **云连接器**"。
1. 选择 "添加 GCP 帐户"。
1. 在 "加入" 页中执行以下操作，然后选择 " **下一步**"。
    1. 验证所选订阅。
    1. 在 " **显示名称** " 字段中，输入连接器的显示名称。
    1. 在 " **组织 id** " 字段中，输入你的组织的 id。 如果你不知道，请参阅 [创建和管理组织](https://cloud.google.com/resource-manager/docs/creating-managing-organization)。
    1. 在 " **私钥** 文件" 框中，浏览到在步骤4中下载的 JSON 文件 [。创建专用服务帐户的私钥](#step-4-create-a-private-key-for-the-dedicated-service-account)。


### <a name="step-6-confirmation"></a>步骤 6。 确认

成功创建连接器并正确配置 GCP 安全命令中心后：

- GCP CIS 标准将显示在安全中心的规章符合性仪表板中。
- GCP 资源的安全性建议将显示在 "安全中心" 门户，并在 "完成" 面板中的 "合规性" 仪表板5-10 分钟内：   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="安全中心建议页中的 GCP 资源和建议":::


## <a name="monitoring-your-gcp-resources"></a>监视 GCP 资源

如上图所示，Azure 安全中心的安全建议页显示 GCP 资源以及 Azure 和 AWS 资源，用于真正的多云视图。

若要按资源类型查看资源的所有活动建议，请使用安全中心的 "资产清单" 页，并筛选到你感兴趣的 GCP 资源类型：

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="显示 GCP 选项的资产清单页面的资源类型筛选器"::: 


## <a name="next-steps"></a>后续步骤

连接 GCP 帐户是 Azure 安全中心提供的多云体验的一部分。 有关相关信息，请参阅以下页面：

- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
