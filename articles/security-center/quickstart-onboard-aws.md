---
title: 将 AWS 帐户连接到 Azure 安全中心
description: 通过 Azure 安全中心监视 AWS 资源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cddae0a7115fc2999b52eaba7df2b49db509981b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449036"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>将 AWS 帐户连接到 Azure 安全中心

由于云工作负载通常跨多个云平台分布，因此云安全服务也需要如此。

Azure 安全中心可保护 Azure、Amazon Web Services (AWS) 和 Google Cloud Platform (GCP) 中的工作负载。

将 AWS 帐户加入安全中心，并将 AWS 安全中心与 Azure 安全中心相集成。 这样安全中心便可同时在这两个云环境中提供可见性和保护，从而能够：

- 自动代理预配（安全中心使用 [Azure Arc](../azure-arc/servers/overview.md) 将 Log Analytics 代理部署到 AWS 实例）
- 策略管理
- 漏洞管理
- 嵌入的终结点检测和响应 (EDR)
- 检测安全配置错误
- 集中通过一个视图显示安全中心建议和 AWS 安全中心的发现结果
- 将 AWS 资源整合到安全中心的安全分数计算中
- AWS 资源的合规性评估

在下面的屏幕截图中，可以看到安全中心的“概述”仪表板中显示了 AWS 帐户。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|需要[用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|所需角色和权限：|相关 Azure 订阅上的“所有者”或“参与者”角色|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||



## <a name="connect-your-aws-account"></a>连接到你的 AWS 帐户

### <a name="step-1-set-up-aws-security-hub"></a>步骤 1。 设置 AWS 安全中心：

1. 若要查看针对多个区域的安全建议，请针对每个相关区域重复以下步骤。

    > [!IMPORTANT]
    > 如果使用的是 AWS 主帐户，请重复以下三个步骤，在所有相关区域中配置主帐户和所有连接的成员帐户

    1. 启用 [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)。
    1. 启用 [AWS 安全中心](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)。
    1. 验证是否存在流向安全中心的数据。

        首次启用安全中心时，可能需要几个小时才能获得数据。

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>步骤 2. 在 AWS 中设置安全中心身份验证

安全中心可以通过两种方式向 AWS 进行身份验证：

- **为安全中心创建 IAM 角色** - 这是最安全的方法，建议使用
- **安全中心的 AWS 用户** - 安全性相对较低的选项（如果不启用 IAM）

#### <a name="create-an-iam-role-for-security-center"></a>为安全中心创建 IAM 角色
1. 在 Amazon Web Services 控制台中，在“安全性、标识与符合性”下选择“IAM”。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::

1. 依次选择“角色”、“创建角色” 。
1. 选择“另一个 AWS 帐户”。
1. 输入以下详细信息：

    - **帐户 ID** - 输入安全中心的 AWS 连接器页中显示的 Microsoft 帐户 ID (**158177204117**)。
    - **需要外部 ID** - 应选择
    - **外部 ID** - 输入安全中心的 AWS 连接器页中显示的订阅 ID 

1. 选择“下一步”  。
1. 在“附加权限策略”部分中，选择以下策略：

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 选择性地添加标记。 将标记添加到用户不会影响连接。
1. 选择“下一步”  。

1. 在“角色”列表中，选择创建的角色

1. 保存 Amazon 资源名称 (ARN) 待稍后使用。 

#### <a name="create-an-aws-user-for-security-center"></a>为安全中心创建 AWS 用户 
1. 打开“用户”选项卡，然后选择“添加用户”。
1. 在“详细信息”步骤中，输入安全中心的用户名，并确保为“AWS 访问类型”选择“编程访问”。 
1. 选择“下一步: 权限”。
1. 选择“直接附加现有策略”并应用以下策略：
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 在完成时选择“下一步:  标记”。 选择性地添加标记。 将标记添加到用户不会影响连接。
1. 选择“查看”。
1. 保存自动生成的访问密钥 ID 和**机密访问密钥** CSV 文件以供稍后使用。
1. 查看摘要，然后单击“创建用户”。


### <a name="step-3-configure-the-ssm-agent"></a>步骤 3. 配置 SSM 代理

跨 AWS 资源自动执行任务需要使用 AWS Systems Manager。 如果 EC2 实例没有 SSM 代理，请按照 Amazon 的相关说明进行操作：

- [在 Windows 实例上安装和配置 SSM 代理](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [在 Amazon EC2 Linux 实例上安装和配置 SSM 代理](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>步骤 4. 创建服务主体以用于大规模加入

以你要用于完成加入过程的订阅上的所有者身份，按照[创建服务主体以用于大规模加入](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)中的说明创建用于 Azure Arc 载入过程的服务主体。


### <a name="step-5-connect-aws-to-security-center"></a>步骤 5。 将 AWS 连接到安全中心

1. 从安全中心的菜单中，选择“多云连接器”。
1. 选择“添加 AWS 帐户”。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::
1. 在“AWS 身份验证”选项卡中配置选项：
    1. 在“显示名称”中输入连接器的显示名称。
    1. 确认订阅是正确的。 这是将要包含连接器和 AWS 安全中心建议的订阅。
    1. 根据之前在[步骤 2：在 AWS 中设置安全中心身份验证](#step-2-set-up-authentication-for-security-center-in-aws)中选择的身份验证选项：
        - 选择“担任角色”，接着粘贴[为安全中心创建 IAM 角色](#create-an-iam-role-for-security-center) :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::中的 ARN

            OR

        - 选择“凭据”，并在其中粘贴[为安全中心创建 AWS 用户](#create-an-aws-user-for-security-center)中保存的 .csv 文件中的访问密钥和机密密钥。
1. 选择“下一步”  。
1. 在“Azure Arc 配置”选项卡中配置选项：

    安全中心在连接的 AWS 帐户中发现 EC2 实例，并使用 SSM 将它们加入 Azure Arc。 

    > [!TIP]
    > 下面的常见问题解答中列出了支持的操作系统。

    1. 选择要在所选订阅中将所发现的 AWS EC2 加入到其中的资源组和 Azure 区域。
    1. 在“服务主体 ID”和“服务主体客户端机密”中输入 Azure Arc 的相应信息，如此处的[创建服务主体以用于大规模载入](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)中所述
    1. 如果计算机是通过代理服务器连接到 internet 的，请指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。 按格式 ```http://<proxyURL>:<proxyport>``` 输入相应值
    1. 选择“查看 + 创建”  。

        查看摘要信息

        “标记”部分会列出将自动为每个载入的 EC2 创建的所有 Azure 标记及其相关详细信息，以便于在 Azure 中轻松识别它。 

        请参阅[使用标记整理 Azure 资源和管理层次结构](../azure-resource-manager/management/tag-resources.md)，详细了解 Azure 标记。

### <a name="step-7-confirmation"></a>步骤 7. 确认

成功创建连接器并正确配置 AWS 安全中心后：

- 安全中心扫描要使用 AWS EC2 实例的环境，将其加入 Azure Arc，以便能够安装 Log Analytics 代理、提供威胁防护和安全建议。 
- ASC 服务每隔 6 小时扫描一次新的 AWS EC2 实例，并根据配置将其加入。
- 安全中心的合规性仪表板中会显示 AWS CIS 标准。
- 如果启用了安全中心策略，加入过程完成 5-10 分钟后，“安全中心”门户和合规性仪表板中会显示建议。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目":::



## <a name="monitoring-your-aws-resources"></a>监视 AWS 资源

如上图所示，Azure 安全中心的安全建议页同时显示了 AWS 资源和 Azure、GCP 资源，呈现了真正的多云视图。

若要按资源类型查看资源的所有活动建议，可使用安全中心的“资产清单”页，并筛选到所需的 AWS 资源类型：

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="安全中心的“概述”仪表板上列出的 3 个 GCP 项目"::: 


## <a name="aws-in-security-center-faq"></a>安全中心中的 AWS 常见问题解答

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>哪些操作系统支持我的 EC2 实例？

支持将 AWS 计算机自动加入 Azure Arc 的操作系统

- Ubuntu 16.04 - 已默认预安装 SSM 代理
- Ubuntu 18.04 - 已默认预安装 SSM 代理
- Windows server - 已默认预安装 SSM 代理
- CentOS Linux 7 - 应手动安装或单独加入 SSM
- SUSE Linux Enterprise Server (SLES) 15 (x64) - 应手动安装或单独加入 SSM
- Red Hat Enterprise Linux (RHEL) 7 (x64) - 应手动安装或单独加入 SSM


## <a name="next-steps"></a>后续步骤

连接 AWS 帐户是 Azure 安全中心提供的多云体验的一部分。 有关相关信息，请参阅以下页面：

- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)
