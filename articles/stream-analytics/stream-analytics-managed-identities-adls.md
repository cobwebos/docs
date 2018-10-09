---
title: 使用托管标识在 Azure Data Lake Storage Gen1 输出（预览版）中对 Azure 流分析作业进行身份验证
description: ''
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2018
ms.openlocfilehash: b79d529822f2b1acca9c8a120202b4ce4010949e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47411376"
---
# <a name="use-managed-identities-to-authenticate-azure-stream-analytics-jobs-to-azure-data-lake-storage-gen1-output-preview"></a>使用托管标识在 Azure Data Lake Storage Gen1 输出（预览版）中对 Azure 流分析作业进行身份验证

Azure 流分析支持使用 Azure Data Lake Storage (ADLS) Gen1 输出进行托管标识身份验证。 标识是 Azure Active Directory 中注册的表示给定流分析作业的托管应用程序，可用于对目标资源进行身份验证。 托管标识消除了基于用户的身份验证方法的如下限制：发生密码更改或用户令牌过期（每隔 90 天过期）时需要重新进行身份验证。 此外，托管标识有助于将输出到 Azure Data Lake Storage Gen1 的流分析作业部署自动化。

请访问 [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/en-us/blog/eight-new-features-in-azure-stream-analytics/)（Azure 流分析中的八项新功能）博客文章来注册此预览版并详细了解新功能。

本文介绍两种为输出到 Azure Data Lake Storage Gen1 的 Azure 流分析作业启用托管标识的方法：通过 Azure 门户，以及通过 Azure 资源管理器模板部署。

## <a name="enable-managed-identity-with-azure-portal"></a>使用 Azure 门户启用托管标识

1. 首先创建新的流分析作业，或在 Azure 门户中打开现有的作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识(预览版)”。

   ![配置流分析托管标识预览版](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 在右侧显示的窗口中选择“使用系统分配的托管标识(预览版)”。 单击“保存”，为 Azure Active Directory 中的流分析作业标识创建服务主体。 新建标识的生命周期将由 Azure 管理。 删除流分析作业时，Azure 会自动删除关联的标识（即服务主体）。

   保存配置后，服务主体的对象 ID (OID) 将列为主体 ID，如下所示：

   ![流分析主体 ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
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

   ![选择权限](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 该服务主体将列在“访问”窗格中“分配的权限”下面，如下所示。 现在，可以返回并启动流分析作业。

   ![访问列表](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   若要详细了解 Data Lake Storage Gen1 文件系统权限，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

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
           "properties": {
             "datasource": {        
   "type": "Microsoft.DataLake/Accounts",
                "properties": {     
                  "accountName": “myDataLakeAccountName",
              "filePathPrefix": “cluster1/logs/{date}/{time}",
              "dateFormat": "YYYY/MM/DD",
              "timeFormat": "HH",
          "authenticationMode": "Msi"
          }
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
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** 是服务主体的对象 ID，创建服务主体后，会在门户屏幕上列出此 ID。 如果使用资源管理器模板部署创建了作业，则对象 ID 将列在作业响应的标识属性中。

   **示例**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   若要详细了解上述 PowerShell 命令，请参阅 [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) 文档。

## <a name="next-steps"></a>后续步骤

* [使用流分析创建 Data Lake Store 输出](../data-lake-store/data-lake-store-stream-analytics.md)