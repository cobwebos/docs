---
title: 将 GCP 帐户连接到 Azure 安全中心
description: 通过 Azure 安全中心监视 GCP 资源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 71f1de7b4ff265a5740181a2bb2032f33a83abe3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448988"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>将 GCP 帐户连接到 Azure 安全中心

由于云工作负载通常跨多个云平台分布，因此云安全服务也需要如此。

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

将 GCP 帐户加入安全中心，并将 GCP 安全命令和 Azure 安全中心相集成。 这样安全中心便可同时在这两个云环境中提供可见性和保护，从而能够：

- 检测安全配置错误
- 集中通过一个视图显示安全中心建议和 GCP 安全命令中心的发现结果
- 将 GCP 资源整合到安全中心的安全分数计算中
- 将基于 CIS 标准的 GCP 安全命令中心建议集成到安全中心的合规性仪表板中

在下面的屏幕截图中，可以看到安全中心的“概述”仪表板中显示了 GCP 项目。

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|所需角色和权限：|相关 Azure 订阅上的“所有者”或“参与者”角色|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="connect-your-gcp-account"></a>连接到你的 GCP 帐户

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>步骤 1。 使用“安全运行状况分析”设置 GCP 安全和命令中心

对于组织中的所有 GCP 项目，还需要：

1. 按照 [GCP 文档中的以下说明](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)设置 GCP 安全命令中心。
1. 按照 [GCP 文档中的以下说明](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)启用“安全运行状况分析”。
1. 验证是否存在流向安全命令中心的数据。

连接 GCP 环境以进行安全配置的相关说明遵循了 Google 有关使用安全配置的建议。 此集成利用 Google 安全命令中心并使用可能会影响计费的其他资源。

首次启用安全运行状况分析时，可能需要几个小时才能获得数据。


### <a name="step-2-enable-gcp-security-command-center-api"></a>步骤 2. 启用 GCP 安全命令中心 API

1. 从 Google 的“云控制台 API 库”中，选择要连接到 Azure 安全中心的项目。
1. 在该 API 库中，查找并选择“安全命令中心 API”。
1. 在 API 的页面上，选择“启用”。

了解有关[安全命令中心 API](https://cloud.google.com/security-command-center/docs/reference/rest/) 的详细信息。


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>步骤 3. 创建一个专用于安全配置集成的服务帐户

1. 在“GCP 控制台”中，选择要连接到安全中心的项目。
1. 在“导航菜单”上的“IAM 和管理”选项下，选择“服务帐户”。
1. 选择“创建服务帐户”。
1. 输入帐户名称，并选择“创建”。
1. 将“角色”指定为“安全中心管理员查看器”，然后选择“继续”。
1. “向用户授予对此服务帐户的访问权限”部分是可选的。 选择“完成”。
1. 复制创建的服务帐户的电子邮件值，并将其保存待稍后使用。
1. 在“导航菜单”上的“IAM 和管理”选项下，选择“IAM”
    1. 切换到组织级别。
    1. 选择“添加”。
    1. 在“新成员”字段中，粘贴之前复制的电子邮件值。
    1. 将“角色”指定为“安全中心管理员查看器”，然后选择“保存”。
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>步骤 4. 为专用服务帐户创建私钥
1. 切换到项目级别。
1. 在“导航菜单”上的“IAM 和管理”选项下，选择“服务帐户”。
1. 打开专用服务帐户，然后选择“编辑”。
1. 在“密钥”部分中，选择“添加密钥”，然后选择“创建新密钥”。
1. 在“创建私钥”屏幕中，选择“JSON”，然后选择“创建”。
1. 保存此 JSON 文件待稍后使用。


### <a name="step-5-connect-gcp-to-security-center"></a>步骤 5。 将 GCP 连接到安全中心 
1. 从安全中心的菜单中，选择“云连接器”。
1. 选择“添加 GCP 帐户”。
1. 在“加入”页中，执行以下操作，然后选择“下一步”。
    1. 验证所选订阅。
    1. 在“显示名称”字段中，输入连接器的显示名称。
    1. 在“组织 ID”字段中，输入组织的 ID。 如果不知道该 ID，请参阅[创建和管理组织](https://cloud.google.com/resource-manager/docs/creating-managing-organization)。
    1. 在“私钥”文件框中，浏览到[步骤 4.为专用服务帐户创建私钥](#step-4-create-a-private-key-for-the-dedicated-service-account)中下载的 JSON 文件。


### <a name="step-6-confirmation"></a>步骤 6。 确认

成功创建连接器并正确配置 GCP 安全命令中心后：

- 安全中心的合规性仪表板中会显示 GCP CIS 标准。
- 加入过程完成 5-10 分钟后，“安全中心”门户和合规性仪表板中会显示有关 GCP 资源的安全性建议： :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::


## <a name="monitoring-your-gcp-resources"></a>监视 GCP 资源

如上图所示，Azure 安全中心的安全建议页同时显示了 GCP 资源和 Azure、AWS 资源，呈现了真正的多云视图。

若要按资源类型查看资源的所有活动建议，可使用安全中心的“资产清单”页，并筛选到所需的 GCP 资源类型：

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目"::: 


## <a name="next-steps"></a>后续步骤

连接 GCP 帐户是 Azure 安全中心提供的多云体验的一部分。 有关相关信息，请参阅以下页面：

- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
