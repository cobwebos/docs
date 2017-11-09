本文中的示例需要版本 3.0 或更高版本的 Azure PowerShell。 如果未安装 3.0 或更高版本，请先使用 PowerShell 库或 Web 平台安装程序[更新版本](/powershell/azureps-cmdlets-docs/)。

若要查看*资源组*的现有标记，请使用：

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

该脚本返回以下格式：

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

若要查看具有指定资源 ID 的资源的现有标记，请使用：

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

或者，若要查看具有指定名称和资源组的资源的现有标记，请使用：

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

若要获取具有特定标记的资源组，请使用：

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

若要获取具有特定标记的资源，请使用：

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含现有标记来使用不同的方法。

若要将标记添加到*不包含现有标记的资源组*，请使用：

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

若要将标记添加到包含现有标记的资源组，请检索现有标记，添加新标记，然后重新应用标记：

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

若要将标记添加到*不包含现有标记的资源*，请使用：

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

若要将标记添加到包含现有标记的资源，请使用：

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

要将资源组中的所有标记应用于其资源，并且*不保留资源上的现有标记*，请使用以下脚本：

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

要将资源组中的所有标记应用于其资源，并且*保留资源上不重复的现有标记*，请使用以下脚本：

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

若要删除所有标记，请传递一个空哈希表：

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
