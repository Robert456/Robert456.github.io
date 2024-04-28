### Admin.net

- 如果只是不想在swagger上显示用` [ApiDescriptionSettings(false)]` ，接口是可以访问的。
- 如果想彻底不允许接口外部访问用[NonAction]

- [NonUnify]去掉全局规范化处理

### vs快捷键

- Ctrl + } = 匹配大括号、括号

- Ctrl + Shift +} = 在匹配的括号、括号内选择文本

- 匹配相同单词并编辑。先选择要编辑的单词或字符，再按一下 `Alt` + `Shift` + `;`，会自动匹配并选中所有相同的单词，然后开始编辑，你也可以使用 `Alt` + `Shift` + `.` 只匹配选择下一个相同的单词。

### SqlSugar方法间的调用实现事务

  ```c#
  //db.Ado.IsNoTran()表示事务为null才开启事务
      using (var uow = db.CreateContext(db.Ado.IsNoTran())) 
      {
           using (var uow2 = db.CreateContext(db.Ado.IsNoTran()))
           { 
              uow2.Commit();
           }
         uow.Commit(); //禁止用 db.RollBack 工作单元内只要throw会自动回滚
       }
  ```

  

### Type

获取 ==Type== 对象的方式取决于你要获取的类型是已知的还是在运行时确定的。

如果你知道要获取的类型的名称，你可以使用 `typeof` 关键字：

```c#
CodeType myType = typeof(MyClass);
```

如果你在运行时才知道要获取的类型的名称，你可以使用 `Type.GetType` 方法：

```c#
Codestring typeName = "MyNamespace.MyClass";
Type myType = Type.GetType(typeName);
```

如果你有一个实例，并想要获取它的类型，你可以使用 `GetType` 方法：

```c#
Codeobject obj = new MyClass();
Type myType = obj.GetType();
```

如果你拥有程序集中的所有类型，并且想要根据类型名称获取类型，你可以使用 `Assembly` 类：

```c#
Codestring typeName = "MyNamespace.MyClass";
Type myType = Assembly.GetExecutingAssembly().GetType(typeName);
```

### Nginx

Websocket报错：Error: Connection disconnected with error 'Error: WebSocket closed with status code: 1006 (no reason given)

CloseStatus=1006的情况

**proxy_read_timeout 默认为60s**

一般出现这种情况都是非正常关闭，此时就需要我们关注这个问题了，解决方案也比较简单：

1. 增大nginx的proxy_read_timeout 时间设置；
2. 减小服务端和客户端之间的心跳间隔；

> ```json
> 		  #signalr 代理
> 		  location /hubs/ {
> 			proxy_pass  http://meszbcfrj;
> 			proxy_read_timeout 90s;  # 设置代理读取响应的超时时间为90秒
> 			proxy_set_header Host $host;
>             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
> 				
> 			#启用http长连接支持websocket
> 			proxy_http_version 1.1;
> 			proxy_set_header Upgrade $http_upgrade;
> 			proxy_set_header Connection "upgrade";		
> 		  }
> ```
>



### SqlServer 强制释放内存

```sql
--强制释放内存  
CREATE procedure [dbo].ClearMemory    
as      
begin  
--清除所有缓存  
DBCC DROPCLEANBUFFERS  
--打开高级配置  
exec sp_configure 'show advanced options', 1  

EXEC ('RECONFIGURE')  
--设置最大内存值，清除现有缓存空间    单位 MB
exec sp_configure 'max server memory', 256  
EXEC ('RECONFIGURE')  
--设置等待时间  
WAITFOR DELAY '00:00:01'  
--重新设置最大内存值  单位 MB
EXEC  sp_configure 'max server memory', 4096  
EXEC ('RECONFIGURE')  
--关闭高级配置  
exec sp_configure 'show advanced options',0  
EXEC ('RECONFIGURE')  

--下面提供内存查看功能的一些脚本语句：

  --内存使用情况
--SELECT * FROM sys.dm_os_performance_counters   
--WHERE counter_name IN ('Target Server Memory (KB)','Total Server Memory (KB)')   

-- 内存状态   ;根据需要显示，也可以不显示
--DBCC MemoryStatus

END
--执行以下语句，释放占用内存

EXEC  ClearMemory
```

