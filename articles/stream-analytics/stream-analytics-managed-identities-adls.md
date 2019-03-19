---
title: 在 Azure Data Lake Storage Gen1 输出中对 Azure 流分析作业进行身份验证
description: 本文介绍如何使用托管标识在 Azure Data Lake Storage Gen1 输出中对 Azure 流分析作业进行身份验证。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 43947413f061ec8b366392b676e848ebf5e6484e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570107"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>使用托管标识在 Azure Data Lake Storage Gen1 中对流分析进行身份验证（预览）

Azure 流分析支持使用 Azure Data Lake Storage (ADLS) Gen1 输出进行托管标识身份验证。 标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序，可用于对目标资源进行身份验证。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 此外，托管标识有助于将输出到 Azure Data Lake Storage Gen1 的流分析作业部署自动化。

请访问 [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)（Azure 流分析中的八项新功能）博客文章来注册此预览版并详细了解新功能。

本文将介绍下面三种方法，用于为输出到 Azure Data Lake Storage Gen1 的 Azure 流分析作业启用托管标识：通过 Azure 门户、Azure 资源管理器模板部署以及适用于 Visual Studio 的 Azure 流分析工具。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure 门户

1. 首先创建新的流分析作业，或在 Azure 门户中打开现有的作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识(预览版)”。

   ![配置流分析托管标识预览版](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 在右侧显示的窗口中选择“使用系统分配的托管标识(预览版)”。 单击**保存**到 Stream Analytics 作业在 Azure Active Directory 中的标识的服务主体。 新建标识的生命周期将由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。

   保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示：

   ![流分析服务主体 ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   服务主体与流分析作业同名。 例如，如果作业的名称是 **MyASAJob**，则创建的服务主体的名称也是 **MyASAJob**。

3. 在 ADLS Gen1 输出接收器的输出属性窗口中，单击“身份验证模式”下拉列表并选择“托管标识(预览版)”。

4. 填写其余的属性。 若要详细了解如何创建 ADLS 输出，请参阅[使用流分析创建 Data Lake Store 输出](../data-lake-store/data-lake-store-stream-analytics.md)。 完成后，单击“保存”。

   ![配置 Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. 导航到 ADLS Gen1 的“概述”页，单击“数据资源管理器”。

   ![配置 Data Lake Storage - 概述](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. 在“数据资源管理器”窗格中选择“访问”，然后在“访问”窗格中单击“添加”。

   ![配置 Data Lake Storage - 访问](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. 在“选择用户或组”窗格中的文本框内，键入服务主体的名称。 请记住，服务主体的名称也是相应流分析作业的名称。 开始键入主体名称时，它会显示在文本框下面。 选择所需的服务主体名称，然后单击“选择”。

   ![选择服务主体名称](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. 在“权限”窗格中，选中“写入”和“执行”权限并将其分配到“此文件夹和所有子文件夹”。 然后单击“确定”。

   ![选择写入和执行权限](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 该服务主体将列在“访问”窗格中“分配的权限”下面，如下所示。 现在，可以返回并启动流分析作业。

   ![门户中的流分析访问列表](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   若要详细了解 Data Lake Storage Gen1 文件系统权限，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

## <a name="stream-analytics-tools-for-visual-studio"></a>适用于 Visual Studio 的流分析工具

1. 在 JobConfig.json 中，将“使用系统分配的标识”设置为“True”。

   ![流分析作业配置托管标识](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. 在 ADLS Gen1 输出接收器的输出属性窗口中，单击“身份验证模式”下拉列表并选择“托管标识(预览版)”。

   ![ADLS 输出托管标识](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. 填写其余的属性，然后单击“保存”。

4. 在查询编辑器中，单击“提交到 Azure”。

   在你提交作业时，这些工具将执行下面两项操作：

   * 在 Azure Active Directory 中为流分析作业的标识自动创建服务主体。 新建标识的生命周期将由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。

   * 为作业中使用的 ADLS Gen1 前缀路径自动设置“写入”和“执行”权限，并将这些权限分配给此文件夹和所有子级。

5. 可以在生成计算机上（在 Visual Studio 之外）使用[流分析 CI.CD Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 或更高版本生成带有以下属性的资源管理器模板。 按照下一节中的资源管理器模板部署步骤获取服务主体，并通过 PowerShell 授予对该服务主体的访问权限。

## <a name="resource-manager-template-deployment"></a>资源管理器模板部署

1. 可以通过在资源管理器模板的 resource 节中包含以下属性，来创建带有托管标识的 *Microsoft.StreamAnalytics/streamingjobs* 资源：

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   此属性告知 Azure 资源管理器为你的 Azure 流分析作业创建和管理标识。

   **示例作业**

    ```json
    {
      "Name": "AsaJobWithIdentity",
      "Type": "Microsoft.StreamAnalytics/streamingjobs",
      "Location": "West US",
      "Identity": {
        "Type": "SystemAssigned",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
        "outputs": [
          {
            "name": "string",
            "properties":{
              "datasource": {
                "type": "Microsoft.DataLake/Accounts",
                "properties": {
                  "accountName": "myDataLakeAccountName",
                  "filePathPrefix": "cluster1/logs/{date}/{time}",
                  "dateFormat": "YYYY/MM/DD",
                  "timeFormat": "HH",
                  "authenticationMode": "Msi"
                }
              }
   ```
  
   **示例作业响应**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
      }
   ```

   记下作业响应中的主体 ID，以授予对所需 ADLS 资源的访问权限。

   “租户 ID”是在其中创建了服务主体的 Azure Active Directory 租户的 ID。 服务主体在订阅信任的 Azure 租户中创建的。

   “类型”表示托管标识的类型（参阅“托管标识的类型”）。 仅支持系统分配的类型。

2. 使用 PowerShell 提供对服务主体的访问权限。 若要通过 PowerShell 授予对服务主体的访问权限，请执行以下命令：

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** 是服务主体的对象 ID，创建服务主体后，会在门户屏幕上列出此 ID。 如果使用资源管理器模板部署创建了作业，则对象 ID 将列在作业响应的标识属性中。

   **示例**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   若要了解有关上述 PowerShell 命令的详细信息，请参阅[集 AzDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)文档。

## <a name="limitations"></a>限制
此功能不支持以下功能：

1.  **多租户访问**:为给定的 Stream Analytics 作业创建的服务主体将驻留在 Azure Active Directory 租户的作业创建，且不能使用对驻留在不同的 Azure Active Directory 租户的资源。 因此，仅可以在同一 Azure Active Directory 租户与 Azure Stream Analytics 作业中的第 1 代 ADLS 资源上使用 MSI。 

2.  **[用户分配标识](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**： 不支持这意味着用户不能输入他们自己的服务主体，以供其 Stream Analytics 作业。 由 Azure Stream Analytics 生成的服务主体。 


## <a name="next-steps"></a>后续步骤

* [使用流分析创建 Data Lake Store 输出](../data-lake-store/data-lake-store-stream-analytics.md)
* [使用 Visual Studio 在本地测试流分析查询](stream-analytics-vs-tools-local-run.md)
* [使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md) 
