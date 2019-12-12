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



