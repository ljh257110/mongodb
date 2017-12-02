# mongodb数据库
### 1.安装
（1）先安装（一路下一步）<br>
（2）再安装（可能提示已安装过）
### 2.建立数据存储的文件夹
在D盘中建立data文件夹，在data中建立db文件夹
### 3.启动服务
（1）找到安装路径```C:\Program Files\MongoDB\Server\3.4\bin```<br>
（2）在bin文件夹中按住shift，右键单击选择"在此处打开命令窗口"<br>
（3）输入```mongod --dbpath D:/data/db```
（4）回车，最后出现27017（端口）即为安装成功(不要关闭，关闭后也以同样的方式打开)<br>
（5）也可在浏览器中打开localhost:27017检测是否安装成功
### 4.使用mongodb
（1）在```C:\Program Files\MongoDB\Server\3.4\bin```中双击打开mongo.exe<br>
（2）输入```show dbs```可查看现有库（库下面为集合，区别于mysql的表）
存在两个默认的库admin，local<br>
（3）输入```use user```可创建一个user库
【注】在未给user添加数据前用show dbs查看现有库时不显示。<br>
（4）输入```db```可查看现在位于哪个库<br>
（5）输入```db.admin```可进入或创建一个在当前库下的集合admin<br>
（6）插入数据：
①输入```db.admin.insertOne({name: 'tomcat'})```可插入一条数据<br>
②输入```db.admin.insert([{name: 'jerry', sex: 'man'}, {username: 'jim', pwd:'123456'}, {name: 'jack', address: {country: 'China', province: 'shandong', aaa: {a1: '1', a2: '2'}}}])```可插入多条数据<br>
（7）查询数据：<br>
①```db.admin.find()```查询所有
//find(查询条件，显示指定属性)<br>
②```db.admin.find({name: 'tomcat'})```查询name为tomcat的数据
//查询, 0不显示，1显示<br>
③```db.admin.find({name: 'zhangsan'},{sex:0,height:0})```查询name为zhangsan的数据，不显示sex和height<br>
④```db.admin.find({name: 'zhangsan'},{name:1,age:1})```查询name为zhangsan的数据，显示name和age<br>
⑤```db.admin.find({name: 'tomcat'}).sort({_id:-1}) ```//排序，1升序， -1降序<br>
⑥```db.admin.find({name: 'tomcat'}).size() ``` //符合条件的数量<br>
（8）修改数据：<br>
【注】第一个参数为修改条件，第二个参数为修改内容，没有的话会创建，使用$set<br>
①修改符合条件的第一条数据
```
	db.admin.update(
	  {name: 'tomcat'},
	  {
	    $set: {email: 'aaa@163.com'}
	  }
	)
```
②修改符合条件的所有数据
```
	db.admin.updateMany(
	  {name: 'mary'},
	  {
	    $set: {age: '22'}
	  }
	)
```
（9）删除数据：
    ①删除符合条件的第一条数据
    ```db.admin.deleteOne({name: 'mary'})```
    ②删除符合条件的所有数据
    ```db.admin.remove({name: 'tomcat'})```
    ③删除库
    ```db.dropDatabase()```
（10）```db.stats()```查询当前库中有多少集合（collections）
（11）```show collections```展示当前库中的集合
### 5.在node.js中连接mongodb
（1）首先本地安装mongodb -D 
```cnpm install mongodb -D```
（2）新建mongodb.js，引入模块、权限认证、创建db的连接
```js
	const mongodb = require('mongodb');
	
	// 权限认证
	var server = new mongodb.Server(
	  'localhost',
	  27017,
	  {auto_reconnect: true} //以自动连接的形式访问数据库
	)
	
	// 创建db的连接
	var db = new mongodb.Db(
	  'user', //所要连接的库
	  server,
	  {safe: true} //以安全形式访问
	)
```
（3）打开数据库、对user下的admin进行操作、增删改查
【注】```conn.find().toArray```可将得到的数据转为数组
forEach可得到数组中的每一个元素
```js
		// 打开数据库
	db.open(function(err, results) {
	  if(err) {
	    console.log(err);
	    return
	  } else {
	    console.log('mongodb success~');
	    //对user下admin进行操作
	    db.collection('admin', {safe: true}, function(err, conn) {
	      if(err) {
	        console.log(err);
	        return
	      } else {
	        console.log(conn);
	        //增删改查
	        conn.find({name: 'jerry'},{sex:0}).toArray(function(err, results) {
	          if(!err) {
	            results.forEach(function(val) {
	              console.log(val);
	            })
	          }
	        })
	      }
	    })
	  }
	})
```
（3）在git中输入```node mongodb.js```,得到两个name为jerry的数据
