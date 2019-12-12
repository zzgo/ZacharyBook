```
SELECT
     table_name       = Case When A.colorder=1 Then D.name Else '' End,
     table_desc     = Case When A.colorder=1 Then isnull(F.value,'') Else '' End,
     colorder   = A.colorder,
     col_name     = A.name,
     cl_desc   = isnull(G.[value],''),
     identity_       = Case When COLUMNPROPERTY( A.id,A.name,'IsIdentity')=1 Then '1'Else '0' End,
     primary_key       = Case When exists(SELECT 1 FROM sysobjects Where xtype='PK' and parent_obj=A.id and name in (
                      SELECT name FROM sysindexes WHERE indid in( SELECT indid FROM sysindexkeys WHERE id = A.id AND colid=A.colid))) then '1' else '0' end,
     col_type       = B.name,
     bit_ = A.Length,
     length       = COLUMNPROPERTY(A.id,A.name,'PRECISION'),
     decimal_   = isnull(COLUMNPROPERTY(A.id,A.name,'Scale'),0),
     null_     = Case When A.isnullable=1 Then '1'Else '0' End,
     default_     = isnull(E.Text,'')
 FROM syscolumns A 
 Left Join systypes B On A.xusertype=B.xusertype
 Inner Join sysobjects D On  A.id=D.id  and D.xtype='U' and  D.name<>'dtproperties' 
 Left Join syscomments E on A.cdefault=E.id
 Left Join sys.extended_properties  G on A.id=G.major_id and A.colid=G.minor_id
 Left Join  sys.extended_properties F On D.id=F.major_id and F.minor_id=0
 where d.name='tb_inst_type'    --如果只查询指定表,加上此条件
 Order By
     A.id,A.colorder
```

```
SELECT     
    表名       = case when a.colorder=1 then d.name else '' end,    
    表说明     = case when a.colorder=1 then isnull(f.value,'') else '' end,    
    字段序号   = a.colorder,    
    字段名     = a.name,    
    标识       = case when COLUMNPROPERTY( a.id,a.name,'IsIdentity')=1 then '√'else '' end,    
    主键       = case when exists(SELECT 1 FROM sysobjects where xtype='PK' and parent_obj=a.id and name in (    
                     SELECT name FROM sysindexes WHERE indid in( SELECT indid FROM sysindexkeys WHERE id = a.id AND colid=a.colid))) then '√' else '' end,    
    类型       = b.name,    
    占用字节数 = a.length,    
    长度       = COLUMNPROPERTY(a.id,a.name,'PRECISION'),    
    小数位数   = isnull(COLUMNPROPERTY(a.id,a.name,'Scale'),0),    
    允许空     = case when a.isnullable=1 then '√'else '' end,    
    默认值     = isnull(e.text,''),    
    字段说明   = isnull(g.[value],'')    
FROM syscolumns a left join systypes b     
on a.xusertype=b.xusertype    
inner join sysobjects d     
on a.id=d.id  and d.xtype='U' and  d.name<>'dtproperties'    
left join syscomments e     
on a.cdefault=e.id    
left join sys.extended_properties   g     
on a.id=g.major_id and a.colid=g.minor_id      
left join sys.extended_properties f    
on d.id=f.major_id and f.minor_id=0    
where d.name= 'tb_inst'    --如果只查询指定表,加上此条件    
order by a.id,a.colorder
```

\[转\]sql server登录名与数据库用户名dbo的差别



SQLSERVER要求在调用函数时,只有返回表值的函数可以不加所有者,否则必须加所有者名称,具体请参阅CREATE   FUNCTION帮助.所以这又是一个我们要遵守的规则. 

至于所有者是不是都是dbo,要看创建这个函数的用户是谁,在这一点上函数与表或存储过程没有任何区别,也就是说所有者就是创建她的用户. 

我们常见的dbo是指以sa\(SQLSERVER登录方式\)或windows   administration\(Windows集成验证登录方式\)登录的用户,也就是说数据库管理员在SQLSERVER中的用户名就叫dbo,而不叫 sa,这一点看起来有点蹊跷,因为通常用户名与登录名相同\(不是强制相同,但为了一目了然通常都在创建用户名时使用与登录名相同的名字\),例如创建了一个 登录,名称为me,那么可以为该登录名me在指定的数据库中添加一个同名用户,使登录名me能够访问该数据库中的数据.当在数据库中添加了一个用户me 后,之后以me登录名登录时在该数据库中创建的一切对象\(表,函数,存储过程等\)的所有者都为me,如 db.me.table1,db.me.fn\_test\(\),而不是dbo. 

不管怎样,只要记住了sa这个登录名对应的用户名是dbo而不是sa就行了. 

另外,不要混淆登录名与用户名.登录名只是具有连接到SQLSERVER的权限,而没有访问SQLSERVER上数据库的权限,所以要为该登录名在指定的 数据库中创建用户名,使其可以访问那个数据库中的数据.一个登录名可以在多个数据库中创建用户名,以使这个登录名能够访问多个数据库\(在登录连接的字符串 中指定默认的数据库名称\),但是一个登录名在一个数据库中只能创建一个对应的用户名,也就是说me登录名在pubs数据库\(举例\)只能创建一个用户名. 

比喻一下:SQLSERVER就象一栋大楼,里面的每个房间都是一个数据库.登录名只是进入大楼的钥匙,而用户名则是进入房间的钥匙.一个登录名可以有多 个房间的钥匙.SQLSERVER把登录名与用户名的关系称为映射. 

至于为什么要使用所有者进行限定,是因为不同的用户可能创建同名的对象,例如登录名me和登录名you在pubs数据库中分别创建了用户名me,和 you,这二个用户都创建了testtable这个同名表,而这二个表虽然同名但结构或数据可能完全不同,为了避免调用错误,必须使用所有者名称进行限 定. 

还有,怎样来调用别的用户创建的对象呢?例如me用户访问you用户创建的表或访问dbo创建的表.此种情况,必须同时满足二个条件: 

1.将me用户的数据库角色设置为db\_owner,否则无法访问其他用户\(包括dbo用户\)创建的对象.\(企业管理器-&gt; 用户,右键菜单 &lt;属性&gt; 中设置\) 

2.使用所有者进行限定. 

例如me访问you创建的testtable: 

select   \*   from   you.testtable 

另外,dbo用户作为管理员,系统赋予其所有的权限,可以调用任何用户创建的对象. 

如果具有db\_owner角色的用户在访问对象时省略了所有者,则系统先查找该用户的对象,若找不到则查找dbo用户是否有同名对象.例如: 

select   \*   from   testtable     或 

select   \*   from   pubs..testtable





