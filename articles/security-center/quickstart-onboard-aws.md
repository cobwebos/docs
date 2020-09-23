---
title: 将 AWS 帐户连接到 Azure 安全中心
description: 监视 Azure 安全中心的 AWS 资源
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ee1b8fe6ed97f3b71cda418cce9e432f7c045447
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934725"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>将 AWS 帐户连接到 Azure 安全中心

云工作负荷通常跨多个云平台，云安全服务必须执行相同的操作。

Azure 安全中心保护 Azure 中的工作负荷，Amazon Web Services (AWS) ，并 Google Cloud Platform (GCP) 。

将 AWS 帐户加入安全中心，将 AWS 安全中心与 Azure 安全中心集成。 因此，安全中心可在这两个云环境之间提供可见性和保护，以提供：

- 自动代理设置 (安全中心使用 [Azure Arc](../azure-arc/servers/overview.md) 将 Log Analytics 代理部署到 AWS 实例) 
- 策略管理
- 漏洞管理
- 嵌入终结点检测和响应 (EDR) 
- 检测安全配置错误
- 显示安全中心建议和 AWS 安全中心发现的单一视图
- 将 AWS 资源合并到安全中心的安全分数计算中
- AWS 资源的法规符合性评估

在下面的屏幕截图中，可以看到在安全中心的 "概述" 仪表板中显示的 AWS 帐户。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3在安全中心的 "概述" 仪表板上列出的 GCP 项目" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|计价|需要 [用于服务器的 Azure Defender](defender-for-servers-introduction.md)|
|必需的角色和权限：|相关 Azure 订阅的**所有者**或**参与者**|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||



## <a name="connect-your-aws-account"></a>连接到你的 AWS 帐户

### <a name="step-1-set-up-aws-security-hub"></a>步骤 1。 设置 AWS 安全中心：

1. 若要查看针对多个区域的安全建议，请针对每个相关区域重复以下步骤。

    > [!IMPORTANT]
    > 如果你使用的是 AWS 主帐户，请重复以下三个步骤，在所有相关区域配置主帐户和所有连接的成员帐户

    1. 启用 [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)。
    1. 启用 [AWS 安全中心](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)。
    1. 验证是否有数据流流向安全中心。

        首次启用安全中心时，数据可能需要几个小时才能使用。

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>步骤 2. 在 AWS 中设置安全中心的身份验证

可以通过两种方式允许安全中心向 AWS 进行身份验证：

- **为安全中心创建 IAM 角色** -这是最安全的方法，建议使用
- **安全中心的 AWS 用户** -如果未启用 IAM，则不安全的选项

#### <a name="create-an-iam-role-for-security-center"></a>为安全中心创建 IAM 角色
1. 在 Amazon Web Services 控制台的 "安全性" 下，选择 " **标识 & 符合性**"，然后选择 " **IAM**"。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 服务":::

1. 选择 " **角色** " 和 " **创建角色**"。
1. 选择 **其他 AWS 帐户**。
1. 输入以下详细信息：

    - **帐户 id** -输入 MICROSOFT 帐户 id (**158177204117**) ，如安全中心的 AWS 连接器页中所示。
    - **需要外部 ID** -应选择
    - **外部 ID** -输入订阅 ID，如安全中心的 AWS 连接器页中所示 

1. 选择“**下一页**”。
1. 在 " **附加权限策略** " 部分中，选择以下策略：

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 选择性地添加标记。 将标记添加到用户不会影响连接。
1. 选择“**下一页**”。

1. 在 "角色" 列表中，选择你创建的角色

1. 保存 Amazon 资源名称 (ARN) 供以后查看。 

#### <a name="create-an-aws-user-for-security-center"></a>为安全中心创建 AWS 用户 
1. 打开 " **用户** " 选项卡，然后选择 " **添加用户**"。
1. 在 " **详细信息** " 步骤中，输入安全中心的用户名，并确保为 AWS 访问类型选择 " **编程访问** "。 
1. 选择“下一步: 权限”。
1. 选择 " **直接附加现有策略** "，并应用以下策略：
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 在完成时选择“下一步:  标记”。 选择性地添加标记。 将标记添加到用户不会影响连接。
1. 选择 " **查看**"。
1. 保存自动生成的 **访问密钥 ID** 和 **密钥访问密钥** CSV 文件以供以后使用。
1. 查看摘要，然后单击 " **创建用户**"。


### <a name="step-3-configure-the-ssm-agent"></a>步骤 3. 配置 SSM 代理

AWS Systems Manager 是在 AWS 资源中自动执行任务所必需的。 如果 EC2 实例没有 SSM 代理，请按照 Amazon 中的相关说明进行操作：

- [在 Windows 实例上安装和配置 SSM 代理](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [在 Amazon EC2 Linux 实例上安装和配置 SSM 代理](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>步骤 4. 创建服务主体以用于大规模加入

作为要用于载入的订阅的**所有者**，请按照[创建服务主体以进行大规模载入](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)中所述为 Azure Arc 载入创建服务主体。


### <a name="step-5-connect-aws-to-security-center"></a>步骤 5。 将 AWS 连接到安全中心

1. 从安全中心的菜单中，选择 " **多云连接器**"。
1. 选择 " **添加 AWS 帐户**"。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="安全中心的 "多云连接器" 页面上的 "添加 AWS 帐户" 按钮":::
1. 在 " **AWS authentication** " 选项卡中配置选项：
    1. 输入连接器的 **显示名称** 。
    1. 确认订阅是正确的。 它是包含连接器和 AWS 安全中心建议的订阅。
    1. 根据你在步骤2中选择的身份验证选项 [。设置 AWS 中安全中心的身份验证](#step-2-set-up-authentication-for-security-center-in-aws)：
        - 在 Azure 门户中，选择 "  **假设角色** 并粘贴 ARN"， [为安全中心 "创建 IAM 角色](#create-an-iam-role-for-security-center)" 将 :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="ARN 文件粘贴到 AWS 连接向导的相关字段中":::

            OR

        - 选择 " **凭据** "，并将 " **访问密钥** **" 和 "密钥"** 粘贴到在 [安全中心的 "创建 AWS 用户](#create-an-aws-user-for-security-center)" 中保存的 .csv 文件。
1. 选择“**下一页**”。
1. 在 " **Azure Arc 配置** " 选项卡中配置选项：

    安全中心发现连接的 AWS 帐户中的 EC2 实例，并使用 SSM 将它们加入 Azure Arc。 

    > [!TIP]
    > 下面的常见问题解答中列出了受支持的操作系统。

    1. 选择已发现的 AWS EC2s 将在所选订阅中载入的 **资源组** 和 **Azure 区域** 。
    1. 输入 "Azure Arc 的 **服务主体 ID** " 和 " **服务主体客户端机密** "，如此处所述为 " [大规模载入" 创建服务主体](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. 如果计算机通过代理服务器连接到 internet，请指定代理服务器的 IP 地址或名称和端口号，以便计算机与代理服务器进行通信。 以格式输入值 ```http://<proxyURL>:<proxyport>```
    1. 选择“查看 + 创建”。

        查看摘要信息

        "标记" 部分会列出将为每个载入 EC2 自动创建的所有 Azure 标记及其相关的详细信息，以便在 Azure 中轻松识别它。 

        了解有关 Azure 标记的详细信息， [请参阅使用标记来组织 azure 资源和管理层次结构](../azure-resource-manager/management/tag-resources.md)。

### <a name="step-7-confirmation"></a>步骤 7. 确认

成功创建连接器并正确配置 AWS 安全中心后：

- 安全中心扫描 AWS EC2 实例的环境，将其载入 Azure Arc，使能够安装 Log Analytics 代理，并提供威胁防护和安全建议。 
- ASC 服务每隔6小时扫描一次新的 AWS EC2 实例，并根据配置对其加入。
- AWS CIS 标准将显示在安全中心的规章符合性仪表板中。
- 如果启用了安全中心策略，则建议会出现在 "安全中心" 门户，并在板载完成后5-10 分钟。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="安全中心的 "建议" 页中的 AWS 资源和建议":::



## <a name="monitoring-your-aws-resources"></a>监视 AWS 资源

如上图所示，Azure 安全中心的安全建议页显示 AWS 资源以及 Azure 和 GCP 资源，用于真正的多云视图。

若要按资源类型查看资源的所有活动建议，请使用安全中心的 "资产清单" 页，并筛选到你感兴趣的 AWS 资源类型：

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="显示 AWS 选项的资产清单页面的资源类型筛选器"::: 


## <a name="aws-in-security-center-faq"></a>安全中心常见问题中的 AWS

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>我的 EC2 实例支持哪些操作系统？

支持的操作系统自动载入 Azure Arc for AWS 计算机

- Ubuntu 16.04-默认情况下，已预安装了 SSM 代理
- Ubuntu 18.04-默认情况下，已预安装了 SSM 代理
- Windows server-在默认情况下预安装了 SSM 代理
- CentOS Linux 7 – SSM 应手动安装或进行单独安装
- SUSE Linux Enterprise Server (SLES) 15 (x64) -SSM 应手动安装，或者单独载入
- Red Hat Enterprise Linux (RHEL) 7 (x64) -SSM 应手动安装或单独载入


## <a name="next-steps"></a>后续步骤

连接 AWS 帐户是 Azure 安全中心提供的多云体验的一部分。 有关相关信息，请参阅以下页面：

- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)
