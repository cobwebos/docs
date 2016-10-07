<properties
   pageTitle="使用 Amazon Web Services 配置身份验证 | Microsoft Azure"
   description="本文介绍如何创建和验证 Azure 自动化管理的 AWS 资源中的 Runbook 的 AWS 凭据。"
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="AWS 身份验证, 配置 AWS"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# 使用 Amazon Web Services 进行 Runbook 身份验证
使用 Amazon Web Services (AWS) 中的资源自动处理常见任务可以通过 Azure 中的自动化 Runbook 完成。可以使用自动化 Runbook 自动处理 AWS 中的许多任务，就如使用 Azure 中的资源可以执行的任务一样。只需要两个事项：

* AWS 订阅和一组凭据。特别是你的 AWS 访问键和密钥。有关详细信息，请查看文章[使用 AWS 凭据](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)。
* Azure 订阅和自动化帐户。有关设置 Azure 自动化帐户的详细信息，请查看文章[配置 Azure 运行方式帐户](../automation/automation-sec-configure-azure-runas-account.md)。

若要使用 AWS 进行身份验证，必须指定一组 AWS 凭据，对从 Azure 自动化运行的 Runbook 进行身份验证。如果已创建了一个自动化帐户，并且想要使用它来通过 AWS 进行身份验证，可以按照以下部分中的步骤进行操作。如果想要一个针对 AWS 资源的专用于 Runbook 的帐户，应首先创建一个新的[自动化运行方式帐户](../automation/automation-sec-configure-azure-runas-account.md)（跳过此选项创建服务主体），然后按照下面的步骤进行操作。

## 配置自动化帐户
若要让 Azure 自动化与 AWS 交流，首先需要检索 AWS 凭据并在 Azure 自动化中将其存储为资产。执行 AWS 文档[管理你的 AWS 帐户的访问键](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)中所述的以下步骤创建访问键，并复制“访问键 ID”和“机密访问键”（选择性地下载密钥文件，将其存储到安全位置）。

创建并复制 AWS 安全密钥后，需要使用 Azure 自动化帐户创建一个凭据资产，以安全将其保存并使用你的 Runbook 对其进行引用。按照 [Azure 自动化中的凭据资产]（../automation/automation-certificates.md/###使用 Azure 门户创建新凭据）文章**创建新凭据**部分中的步骤进行操作，然后输入以下信息：

1. 在“名称”框中，输入 **AWScred** 或遵循命名标准的适当值。
2. 在“用户名”框中，键入你的“访问 ID”，并在“密码”和“确认密码”框中键入“机密访问键”。

## 后续步骤

- 查看解决方案文章 [Amazon Web Services 中 VM 的自动化部署](../automation/automation-scenario-aws-deployment.md)，了解如何创建 Runbook，自动化 AWS 中的任务。

<!---HONumber=AcomDC_0921_2016-->