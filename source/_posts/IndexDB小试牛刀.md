---
title: IndexDB小试牛刀
date: 2020-11-16 20:15:53
tags: indexedDB
cover: ./img/6indexdb.jpg
description: indexdb入门
---
### IndexDB
#### 1.基本概念
IndexBD里把不同的实体抽象成一个个对象接口,需要学习的是其中的对象接口;
>+ 数据库 - IDBDatabase 对象   
是一系列相关数据的存储容器,每个域名(协议+域名+端口)可以创建任意多个数据库;   
数据库有版本的概念,如果要修改数据库的结构(新增/删除表,索引,主键),只能通过升级数据库版本来实现;
>+ 对象仓库 - IDBObjectStore 对象   
类似数据库里的表格概念,每个数据库可以包含若干个对象仓库;    
对象仓库保存的是数据记录,是表格里的行,但其只有主键和数据体两部分;    
主键用来建立默认索引,可以使数据记录里的一个属性也可以是递增的整数,必须是不同的值;数据体可以使任意数据类型;
>+ 索引 - IDBIndex 对象
加速数据检索
>+ 事务 - IDBTransaction 对象
数据的增删改查都要通过事务完成,事务对象提供 error, about, success 三个事件,用来监听操作结果;
>+ 操作请求 - IDBRequest 对象
>+ 指针 - IDBCursor 对象
>+ 主键集合 - IDBKeyRange 对象
#### 2.操作流程
>1.新建/打开数据库 .open(databaseName, \[version]);     
该方法返回 IDBRequest 对象,通过 error, success, upgradeneeded 三个事件处理打开数据库的操作请求;    
若指定打开的数据库不存在,就会该数据库,并触发 upgradeneeded 事件;    
新建数据库以后, 创建数据表即对象仓库,创建前可以通过访问数据库变量的 objectStoreNames 来检查该对象仓库是否已经存在;
新建对象仓库之后,新建索引

```
let db = null;
/**------新建/打开数据库-----------------------------------**/
let request = window.indexedDB.open('testIDB');   //打开数据库,若不存在即创建;
request.onerror = (event) => {
    console.log('打开数据库失败');
}
request.onsuccess = (event) => {
    db = request.result;     //通过result获取数据库对象
    console.log('打开数据库成功');
}
request.onupgradeneeded = (event) =>{
    //若指定版本号大于数据库实际版本号,会触发该事件
    db = event.target.result;    //通过target.result属性拿到数据库对象;
    if (!db.objectStoreNames.containers('person')) {
        db.createObjectStore('person', {autoIncrement: true});  // 主键递增的表
    }
    if (!db.objectStoreNames.containers('testTable')) {
        db.createObjectStore('testTable', {keyPath: id});   //主键为id的表
    }
    person.createIndex('name', 'name', {unique: false})    //创建索引
}
```
>2. 增查删改数据 add / get / delete / put   
增查删改改变对象仓库的操作,需要通过事务对象 (IDBTransaction) 完成   
add / get / delete / put 方法返回一个连接对象,通过监听该对象的 error, success 事件判断操作数据的结果;
```
let oprtDate = (type) => {
    let person_transaction = db.transaction(['person'], 'readwrite');    //创建事务对象,指定表格名称和操作类型
    let person_os = person_transaction.objectStore('person');   //获取到对象仓库对象
    let add_request;
    let oprt_request;
    switch(type) {
        case 'add':   //新增数据
            oprt_request = person_os.add({id: 1, name: 'name1', age: 'age1'});
            break;
        case 'get':  //获取主键为1的数据
            oprt_request = person_os.get(1); 
            break;
        case 'put':    //修改主键为1的数据
            oprt_request = person_os.put({id: 1, name: 'real11', age: 11});
             break;
        case 'delete':   //删除主键为1的数据
            oprt_request = person_os.delete(1);
            break;
    }
    oprt_request.onerror = () => {   //操作失败
        console.log(type + '失败');
    }
    oprt_request.onsuccess = () => {   //操作成功
        console.log('写入数据成功');
        if (type === 'get') {
            console.log(oprt_request.result);
        }
    }
}
```
>3. 遍历数据   
遍历表格的所有数据,同样需要在事务对象上完成, 并使用指针对象 IDBCursor 的 openCursor 方法, 需要监听其 success 事件
```
let readAll = () => {
        let transaction = db.transaction(['person'], 'readwrite');
        let person_os = transaction.objectStore('person');
        let readall_req = person_os.openCursor();    //创建指针
        readall_req.onsuccess = (event) => {
            let cursor = event.target.result;
            if (cursor) {
                console.log(cursor.key);
                console.log(cursor.value);
                cursor.continue();
            } else {
                console.log('读取完毕');
            }
        }
    }
```
>4. 使用索引   
默认情况下只能使用主键进行搜索, 建立索引,可以搜索任意字段;   
首先,新建表格(即仓库对象)的时候对某个字段建立索引, 在获取数据记录时,就可以使用该字段为索引,进行搜索;   
```
//在创建表格是对name字段建立了索引
person.createIndex('name', 'name', {unique: false});
let name_index = person_os.index('name');
let hobby_index = person_os.index('hobby', {unique: false, multiEntry: true});   //创建hobby索引,可以获取hobby里包含某个值的所有数据
let get_request = name_index.get('name1')   //获取name值为name1的数据记录
```
>5. 使用范围
使用范围和使用游标不同,范围是在索引上打开游标,而不是在对象存储上;IDBKeyRange 提供了 upperBound, lowerBound, bound 和 only 函数, 分别用来指定上限, 下限, 包含上下限的一个范围和某个指定值;默认为闭区间,通过方法的第二个参数 false 来制定开区间.但IndexedDB不擅长做复杂的检索.
```
let range = IDBKeyRange.upperBond('name1');   //设置上限name1,范围是小于/低于name1的数据
let cursor = name_index.openCursor(range, "prev") //指定游标方向,默认是forward
```
>6. 计算数据量   
不用使用游标遍历整个表,使用 count 方法
```
db.transaction(['person'], 'readonly').objectStore('person').count() = (event) => {
    console.log("总计" + event.target.result + "条数据");
}
```
以上文章参考[阮一峰indexDB入门教程](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html),跟着写了一遍操作的代码;


