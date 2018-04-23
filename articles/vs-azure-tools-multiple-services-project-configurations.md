---
title: 使用多种服务配置来配置 Azure 项目 | Microsoft Docs
description: 了解如何通过更改 ServiceDefinition.csdef、ServiceConfiguration.Local.cscfg 和 ServiceConfiguration.Cloud.cscfg 文件来配置 Azure 云服务项目。
services: visual-studio-online
author: ghogen
manager: douge
assetId: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 9047d7a8a6efdd41a48b6fa83b43a8c87d05d1de
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>使用多种服务配置来配置 Visual Studio 中的 Azure 项目

Visual Studio 中的 Azure 云服务项目包括三个配置文件：`ServiceDefinition.csdef`、`ServiceConfiguration.Local.cscfg` 和 `ServiceConfiguration.Cloud.cscfg`。

- `ServiceDefinition.csdef` 部署到 Azure 来描述云服务及其角色的要求，并提供适用于所有实例的设置。 可以在运行时使用 Azure 服务托管运行时 API 读取这些设置。 只有当云服务停止时，才可以在 Azure 上更新此文件。
- `ServiceConfiguration.Local.cscfg` 和 `ServiceConfiguration.Cloud.cscfg` 为定义文件中的设置提供值，并为每个角色指定要运行的实例数。 “本地”文件包含在本地调试中使用的值；“云”文件作为 `ServiceConfiguration.cscfg` 部署到 Azure，并提供针对服务器环境的设置。 在 Azure 中运行云服务时，可以更新此文件。

在 Visual Studio 中，使用适用角色的属性页面来管理和修改配置设置（右键单击角色并选择“属性”，或双击角色）。 可将更改范围限定为在“服务配置”下拉列表中所选择的任何配置。 除了以下各节所描述的内容以外，Web 角色和辅助角色的属性是很相似的。

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

有关服务定义文件和服务配置文件的基础架构的信息，请参阅 [.csdef XML 架构](cloud-services/schema-csdef-file.md)和 [.cscfg XML 架构](cloud-services/schema-cscfg-file.md)文章。 有关服务配置的详细信息，请参阅[如何配置云服务](cloud-services/cloud-services-how-to-configure-portal.md)。


## <a name="configuration-page"></a>配置页

### <a name="service-configuration"></a>服务配置

选择受更改影响的 `ServiceConfiguration.*.cscfg` 文件。 默认情况下有本地和云变体，并且可以使用“管理...”命令来复制、重命名和删除配置文件。 这些文件会被添加到你的云服务项目，并显示在“解决方案资源管理器”中。 但是，只能从该控件中完成重命名或删除配置操作。

### <a name="instances"></a>Instances

将“实例计数”属性设置为服务应为此角色运行的实例的数目。

将“VM 大小”属性设置为“特小”、“小”、“中”、“大”或“特大”。  有关详细信息，请参阅[云服务的大小](cloud-services/cloud-services-sizes-specs.md)。

### <a name="startup-action-web-role-only"></a>启动操作（仅适用于 Web 角色）

设置此属性可指定在开始调试时，Visual Studio 应为 HTTP 终结点、HTTPS 终结点还是两者启动 Web 浏览器。

仅在为角色定义了 HTTPS 终结点时，“HTTPS 终结点”选项才可用。 可以在“终结点”属性页上定义 HTTPS 终结点。

如果已添加 HTTPS 终结点，则将默认启用“HTTPS 终结点”选项，Visual Studio 除了为 HTTP 终结点启动浏览器之外，还会在开始调试时为此终结点启动浏览器，假定这两个启动选项均已启用。

### <a name="diagnostics"></a>诊断

默认情况下，将对 Web 角色启用诊断。 Azure 云服务项目和存储帐户都设置为使用本地存储模拟器。 准备好部署到 Azure 后，可以选择生成器按钮 (…) 以改用 Azure 存储。 可以根据需要或自动计划的间隔，将诊断数据传输到存储帐户。 有关 Azure 诊断的详细信息，请参阅[在 Azure 云服务和虚拟机中启用诊断](cloud-services/cloud-services-dotnet-diagnostics.md)。

## <a name="settings-page"></a>“设置”页

在“设置”页上，可以将设置以名称/值对形式添加到配置中。 角色中运行的代码可以使用 [Azure 托管库](http://go.microsoft.com/fwlink?LinkID=171026)提供的类（具体而言，[GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) 方法），在运行时读取配置设置的值。

### <a name="configuring-a-connection-string-for-a-storage-account"></a>为存储帐户配置连接字符串

连接字符串是一个设置，它为存储模拟器或 Azure 存储帐户提供连接和身份验证信息。 每当角色中的代码访问 Azure 存储（blob、队列或表）时，它就需要一个连接字符串。

> [!Note]
> Azure 存储帐户的连接字符串必须使用定义的格式（请参阅[配置 Azure 存储连接字符串](storage/common/storage-configure-connection-string.md)）。

你可以根据需要设置连接字符串以使用本地存储，然后在将应用程序部署到云服务时将其设置为 Azure 存储帐户。 未能正确设置连接字符串可能会导致角色无法启动，或是在初始化、忙碌、停止状态之间循环。

若要创建连接字符串，请选择“添加设置”，并将“类型”设置为“连接字符串”。

对于新的或现有连接字符串，选择“值”字段右侧的“...”*，以打开“创建存储连接字符串”对话框：

1. 在“连接时使用”下，选择“你的订阅”选项，以从订阅中选择存储帐户。 然后 Visual Studio 会从 `.publishsettings` 文件自动获取存储帐户凭据。
1. 选择“手动输入凭据”，以便直接使用来自 Azure 门户的信息指定帐户名和密钥。 复制帐户密钥：a。 导航到 Azure 门户上的存储帐户，然后选择“管理密钥”。
    2. 要复制帐户密钥，请导航到 Azure 门户中的存储帐户，选择“设置”>“访问密钥”，然后使用“复制”按钮将主访问密钥复制到剪贴板。
1. 选择其中一个连接选项。 “指定自定义终结点”会要求你指定 blob、表和队列的特定 URL。 自定义终结点允许你使用[自定义域](storage/blobs/storage-custom-domain-name.md)以及更精确地控制访问权限。 请参阅[配置 Azure 存储连接字符串](./storage/common/storage-configure-connection-string.md)。
1. 选择“确定”，然后选择“文件 > 保存”，以使用新的连接字符串更新配置。

同样，将应用程序发布到 Azure 时，请选择包含连接字符串的 Azure 存储帐户的服务配置。 发布应用程序后，验证应用程序是否按预期方式针对 Azure 存储服务正常工作。

有关如何更新服务配置的详细信息，请参阅[管理存储帐户的连接字符串](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts)部分。

## <a name="endpoints-page"></a>“终结点”页

Web 角色通常在端口 80 上具有单个 HTTP 终结点。 另一方面，一个辅助角色可以有任意数量的 HTTP、HTTPS 或 TCP 终结点。 终结点可以是供外部客户端使用的输入终结点，也可以是供服务中其他运行角色使用的内部终结点。

- 要使 HTTP 终结点可用于外部客户端和 Web 浏览器，请将终结点类型更改为输入，然后指定名称和公用端口号。
- 要使 HTTPS 终结点可用于外部客户端和 Web 浏览器，请将终结点类型更改为“输入”，然后指定名称、公用端口号和管理证书名称。 请注意，必须先在“证书”属性页上定义一个证书，才能指定管理证书。 
- 要使终结点可供云服务中的其他角色进行内部访问，请将终结点类型更改为内部，然后为其指定名称和可行的专用端口。

## <a name="local-storage-page"></a>“本地存储”页

可以使用“本地存储”属性页来保留某个角色的一个或多个本地存储资源。 本地存储资源是运行角色实例的 Azure 虚拟机的文件系统中的保留目录。

## <a name="certificates-page"></a>“证书”页

“证书”属性页会将有关证书的信息添加到服务配置。 请注意，证书不会与服务一起打包；必须通过 [Azure 门户](http://portal.azure.com)将证书单独上传到 Azure。

在此添加证书会将有关证书的信息添加到服务配置。 证书不会随服务一起打包；必须通过 Azure 门户单独上传证书。

要将证书与角色关联，请提供证书的名称。 在“终结点”页面上配置 HTTPS 终结点时，将使用此名称来引用证书。 接下来，指定证书存储是“本地计算机”还是“当前用户”，并指定存储的名称。 最后，输入证书的指纹。 如果证书位于“当前用户\个人(我的)”存储区中，可以通过从填充列表中选择证书来输入证书的指纹。 如果证书驻留在任何其他位置，则手动输入指纹值。

添加来自证书存储区中的证书时，系统会自动将任何中间证书添加到配置设置。 此外，要正确为服务配置 SSL，必须将这些中间证书上传到 Azure。

仅当服务在云中运行时，与该服务关联的所有管理证书才会应用到该服务。 当服务在本地开发环境中运行时，它使用由计算模拟器管理的标准证书。
