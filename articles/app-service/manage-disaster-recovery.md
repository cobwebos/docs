---
title: 从区域范围的故障中恢复
description: 了解 Azure App Service 如何帮助你维护业务连续性和灾难恢复（BCDR）功能。 从 Azure 中的区域范围故障中恢复应用。
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 8c57cf5054bea898370cdccc7bea4243877d27b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84946899"
---
# <a name="move-an-app-service-app-to-another-region"></a>将应用服务应用移到另一个区域

本文介绍如何在影响整个 Azure 区域的灾难发生时，使应用服务资源在不同的 Azure 区域中恢复联机。 当发生灾难时，所有 Azure 区域都处于脱机状态时，将在灾难恢复模式下放置在该区域中托管的所有应用服务应用。 提供的功能可帮助你将应用程序还原到不同的区域，或从受影响的应用程序恢复文件。

应用服务资源是特定于区域的，不能跨区域移动。 必须将应用还原到其他区域中的新应用，然后为新应用创建镜像配置或资源。

## <a name="prerequisites"></a>先决条件

- 无。 [从快照还原](app-service-web-restore-snapshots.md)通常需要**高级**级别，但在灾难恢复模式中，它会自动为受影响的应用程序启用，而不考虑受影响的应用程序所在的层。

## <a name="prepare"></a>准备

确定受影响的应用当前使用的所有应用服务资源。 例如：

- 应用服务应用
- [应用服务计划](overview-hosting-plans.md)
- [部署槽](deploy-staging-slots.md)
- [在 Azure 中购买的自定义域](manage-custom-dns-buy-domain.md)
- [SSL 证书](configure-ssl-certificate.md)
- [Azure 虚拟网络集成](web-sites-integrate-with-vnet.md)
- [混合连接](app-service-hybrid-connections.md)。
- [托管标识](overview-managed-identity.md)
- [备份设置](manage-backup.md)

某些资源（如导入的证书或混合连接）包含与其他 Azure 服务的集成。 有关如何跨区域移动这些资源的信息，请参阅相应服务的文档。

## <a name="restore-app-to-a-different-region"></a>将应用还原到不同的区域

1. 在与受影响的应用*不同*的 Azure 区域中创建新的应用服务应用。 这是灾难恢复方案中的目标应用。

1. 在[Azure 门户](https://portal.azure.com)中，导航到受影响的应用的 "管理" 页。 在 Azure 区域发生故障时，受影响的应用会显示警告文本。 单击警告文本。

    ![](media/manage-disaster-recovery/restore-start.png)

1. 在 "**还原备份**" 页中，根据下表配置还原操作。 完成后，单击“确定”****。

   | 设置 | “值” | 描述 |
   |-|-|-|
   | **快照（预览版）** | 选择快照。 | 可以使用两个最新的快照。 |
   | **还原目标** | **现有应用** | 单击下面的注释 "**单击此处以更改还原目标应用程序**并选择目标应用程序"。 在灾难情况下，只能将快照还原到不同 Azure 区域中的应用。 |
   | **还原站点配置** | **是** | |

    ![](media/manage-disaster-recovery/restore-configure.png)

3. 在目标应用中配置[其他所有内容](#prepare)以镜像受影响的应用并验证你的配置。

4. 当你已准备好将自定义域指向目标应用时，将重新[映射该域名](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

## <a name="recover-app-content-only"></a>仅恢复应用内容

如果只想在不还原受影响的应用程序的情况下恢复文件，请使用以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，导航到受影响的应用的 "管理" 页，然后单击 "**获取发布配置文件**"。

    ![](media/manage-disaster-recovery/get-publish-profile.png)

1. 打开下载的文件，并查找其名称中包含的发布配置文件 `ReadOnly - FTP` 。 这是灾难恢复配置文件。 例如：

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    复制三个属性值： 
        
    - `publishUrl`： FTP 主机名
    - `userName`和 `userPWD` ： FTP 凭据

1. 使用所选的 FTP 客户端，连接到受影响的应用的 FTP 主机，使用主机名和凭据。

1. 连接后，下载整个 */site/wwwroot*文件夹。 以下屏幕截图显示了如何在[FileZilla](https://filezilla-project.org/)中下载。

    ![](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>后续步骤
[在 Azure 中从快照还原应用](app-service-web-restore-snapshots.md)
