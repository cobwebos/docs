AzureRm.资源模块版本 3.0 在标记的用法方面做了重大更改。 在继续学习之前，请检查你的版本：

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

如果结果显示版本 3.0 或以上，则本主题中的示例适用于你的环境。 如果未安装版本 3.0 或以上，请先使用 PowerShell 库或 Web 平台安装程序[更新版本](/powershell/azureps-cmdlets-docs/)，然后继续学习本主题。

```powershell
Version
-------
3.5.0
```

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含想要保留的现有标记来使用不同的方法。 若将标记添加到：

* 不包含现有标记的资源组。

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* 包含现有标记的资源组。

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* 不包含现有标记的资源。

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* 包含现有标记的资源。

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

若要将资源组中的所有标记应用于其资源，并且**不保留资源上的现有标记**，请使用以下脚本：

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

若要将资源组中的所有标记应用于其资源，并且**保留资源上不重复的现有标记**，请使用以下脚本：

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

若要删除所有标记，请传递一个空哈希表。

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

若要获取具有特定标记的资源组，请使用 `Find-AzureRmResourceGroup` cmdlet。

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

若要获取具有特定标记和值的所有资源，请使用 `Find-AzureRmResource` cmdlet。

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

