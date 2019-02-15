### redis事务

pipeline是多条命令的组合，为了保证它的原子性，redis提供了简单的事务

redis简单的事务，将一组需要一起执行的命令放到multi和exec两个命令之间，其中multi代表事务的开始，exec代表事务结束

watch命令：使用watch后，multi失效，事务失效

### 总结

redis 提供了简单的事务，不支持事务回滚（有些时候能，有些时候不能）



