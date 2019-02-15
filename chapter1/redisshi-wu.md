### redis事务

pipeline是多条命令的组合，为了保证它的原子性，redis提供了简单的事务

redis简单的事务，将一组需要一起执行的命令放到multi和exec两个命令之间，其中multi代表事务的开始，exec代表事务结束

watch命令：使用watch后，multi失效，事务失效

### 总结

redis 提供了简单的事务，不支持事务回滚

### 实战操作

##### multi事务开启

```
127.0.0.1:6379> multi                       #事务开始
OK
127.0.0.1:6379> sadd user:name james        #业务操作1
QUEUED
127.0.0.1:6379> set user:age 24             #业务操作2
QUEUED
127.0.0.1:6379> get user:age                #若在另一个客户端获取user:age,返回QUEUED，事务还没有结束
QUEUED
127.0.0.1:6379> exec                        #事务结束
1) (integer) 1
2) (integer) 1
3) (error) WRONGTYPE Operation against a key holding the wrong kind of value #由于在事务中使用了get命令
127.0.0.1:6379> get user:age                    #此时再能查看数据
24
```

注意：**在事务中使用get 命名是查询不到数据的（不管是当前客户端，还是另一个客户端），必须等到事务提交了，才可以查询到数据**

##### 停止事务discard

```
127.0.0.1:6379> multi        #事务开始
OK
127.0.0.1:6379> set tt 1     #业务操作        
QUEUED
127.0.0.1:6379> discard      #停止事务  
OK
127.0.0.1:6379> exec
(error) ERR EXEC without MULTI
127.0.0.1:6379> get tt       #查不到数据
(nil)
```

##### 命名错误，语法不正确，导致事务不能正常结束

```
127.0.0.1:6379> multi                #事务开始
OK
127.0.0.1:6379> set aa 123           #正确业务操作
QUEUED
127.0.0.1:6379> sett bb 234          # 错误指令  
(error) ERR unknown command 'sett'
127.0.0.1:6379> exec                 #事务提交无效
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get aa               #查不到数据
(nil)
```

##### 运行错误，语法正确，但类型错误，事务可以正常结束

```
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set t 1    #设置key=t，值时整形
QUEUED
127.0.0.1:6379> sadd t 1
QUEUED
127.0.0.1:6379> set t 2
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) WRONGTYPE Operation against a key holding the wrong kind of value
3) OK
127.0.0.1:6379> get t
"2"
```



