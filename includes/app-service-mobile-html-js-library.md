## <a name="create-client"></a>创建客户端连接
创建客户端的连接创建`WindowsAzure.MobileServiceClient`对象。  替换`appUrl`使用你的移动应用的 URL。

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>使用表
访问或更新数据，创建对后端表的引用。 替换`tableName`与你的表名称

```
var table = client.getTable(tableName);
```

表引用后，您可以进一步与您的表：

* [查询表](#querying)
  * [筛选数据](#table-filter)
  * [通过数据的分页](#table-paging)
  * [对数据进行排序](#sorting-data)
* [插入数据](#inserting)
* [修改数据](#modifying)
* [删除数据](#deleting)

### <a name="querying"></a>如何： 查询的表引用
表引用后，可用于查询有关在服务器上的数据。  在"类似的 LINQ"语言进行查询。
若要返回表中的所有数据，请使用下面的代码：

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Success 函数调用的结果。  不要使用`for (var i in results)`获得成功，将循环访问结果中包括的信息发挥时其他查询函数 (如`.includeTotalCount()`) 使用。

有关查询语法的详细信息，请参阅 [查询对象文档]。

#### <a name="table-filter"></a>在服务器上的筛选数据
你可以使用`where`子句对表引用：

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

你还可以使用筛选器对象的函数。  在这种情况下，`this`变量分配给要筛选的当前对象。  下面的代码在功能上等效于前一个示例：

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>到数据进行分页
利用`take()`和`skip()`方法。  例如，如果你想要拆分为 100 行记录的表：

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()`方法用于将 totalCount 字段添加到结果对象。  TotalCount 字段填入的如果使用没有分页，则将返回的记录总数。

你可以然后使用页变量和一些 UI 按钮提供的页列表;使用`loadPage()`加载每个页面的新记录。  实现对已加载的记录的速度访问缓存。

#### <a name="sorting-data"></a>如何： 返回已排序数据
使用`.orderBy()`或`.orderByDescending()`查询方法：

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

查询对象的详细信息，请参阅 [查询对象文档]。

### <a name="inserting"></a>如何： 插入数据
创建具有相应的日期和调用的 JavaScript 对象`table.insert()`以异步方式：

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

成功插入与所需的同步操作的其他字段被返回插入的项。  使用更高版本更新此信息更新您自己的缓存。

Azure 移动应用程序 Node.js 服务器 SDK 出于开发目的支持动态架构。  动态架构，可将列添加到表中，通过指定中插入或更新操作。  我们建议，动态架构之前关闭移动到生产环境的应用程序。

### <a name="modifying"></a>如何： 修改数据
类似于`.insert()`方法，你应创建更新对象，然后调用`.update()`。  更新对象必须包含要更新的记录 ID-读取记录时或在调用时获取该 ID `.insert()`。

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>如何： 删除数据
若要删除记录时，调用`.del()`方法。  将 ID 传递对象引用中：

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
