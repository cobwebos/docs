---
title: 在发生区域范围的故障后进行恢复
description: 了解 Azure 应用服务如何帮助你维护业务连续性和灾难恢复 (BCDR) 功能。 在 Azure 中发生区域范围的故障后恢复你的应用。
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1388dc11254324f74efcbaa55c97cac2ccd0c026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073746"
---
# <a name="move-an-app-service-app-to-another-region"></a>将应用服务应用移动到其他区域

本文介绍了当发生影响整个 Azure 区域的灾难时，如何使应用服务资源在另一个 Azure 区域中恢复联机。 当灾难导致整个 Azure 区域都处于脱机状态时，该区域中承载的所有应用服务应用都将被置于灾难恢复模式。 提供的功能可帮助你将应用还原到其他区域或从受影响的应用中恢复文件。

应用服务资源是特定于区域的，不能跨区域移动。 你必须将应用还原为其他区域中的新应用，然后为新应用创建镜像配置或资源。

## <a name="prerequisites"></a>先决条件

- 无。 [从快照进行还原](app-service-web-restore-snapshots.md)通常需要“高级”层，但在灾难恢复模式下，系统会自动为受影响的应用启用此功能，不管受影响的应用在哪个层中。

## <a name="prepare"></a>准备

查明受影响的应用当前使用的所有应用服务资源。 例如：

- 应用服务应用
- [应用服务计划](overview-hosting-plans.md)
- [部署槽](deploy-staging-slots.md)
- [在 Azure 中购买的自定义域](manage-custom-dns-buy-domain.md)
- [SSL 证书](configure-ssl-certificate.md)
- [Azure 虚拟网络集成](web-sites-integrate-with-vnet.md)
- [混合连接](app-service-hybrid-connections.md)。
- [托管标识](overview-managed-identity.md)
- [备份设置](manage-backup.md)

某些资源（例如导入的证书或混合连接）包含与其他 Azure 服务的集成。 有关如何跨区域移动这些资源的信息，请参阅相应服务的文档。

## <a name="restore-app-to-a-different-region"></a>将应用还原到其他区域

1. 在与受影响的应用不同的 Azure 区域中创建一个新的应用服务应用。 这是灾难恢复方案中的目标应用。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到受影响应用的管理页面。 在发生故障的 Azure 区域中，受影响的应用会显示警告文本。 单击该警告文本。

    ![受影响应用的页面的屏幕截图。 此时会显示一条警告通知，其中描述了这种情况，并提供了用于还原应用的链接。](media/manage-disaster-recovery/restore-start.png)

1. 在“还原备份”页面中，根据下表配置还原操作。 完成后，单击 **“确定”** 。

   | 设置 | 值 | 描述 |
   |-|-|-|
   | **快照（预览版）** | 选择一个快照。 | 有两个最新的快照可用。 |
   | **还原目标** | **现有应用** | 单击下方的“单击此处更改还原目标应用”说明，并选择目标应用。 在灾难场景中，只能将快照还原到其他 Azure 区域中的应用。 |
   | **还原站点配置** | **是** | |

    ![“还原备份”页面的屏幕截图。 一个具体的快照，其中突出显示了上表列出的选项和“确定”按钮。](media/manage-disaster-recovery/restore-configure.png)

3. 在目标应用中配置[其他所有事项](#prepare)，以镜像受影响的应用并验证你的配置。

4. 当你已准备好将自定义域指向目标应用时，[重新映射域名](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

## <a name="recover-app-content-only"></a>仅恢复应用内容

如果只想从受影响的应用恢复文件而不还原它，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，导航到受影响应用的管理页面，然后单击“获取发布配置文件”。

    ![受影响应用的页面的屏幕截图。 显示了一条警告通知，但未突出显示它， 而是突出显示了“获取发布配置文件”项。](media/manage-disaster-recovery/get-publish-profile.png)

1. 打开下载的文件，查找名称中包含 `ReadOnly - FTP` 的发布配置文件。 这是灾难恢复配置文件。 例如：

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    复制三个属性值： 
        
    - `publishUrl`：FTP 主机名
    - `userName` 和 `userPWD`：FTP 凭据

1. 使用你选择的 FTP 客户端，使用主机名和凭据连接到受影响应用的 FTP 主机。

1. 连接后，下载整个 /site/wwwroot 文件夹。 以下屏幕截图显示了如何在 [FileZilla](https://filezilla-project.org/) 中下载。

    ![FileZilla 文件层次结构的屏幕截图。 突出显示了 wwwroot 文件夹并显示了其快捷菜单。 在该菜单中，突出显示了“下载”。](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>后续步骤
[在 Azure 中从快照还原应用](app-service-web-restore-snapshots.md)
