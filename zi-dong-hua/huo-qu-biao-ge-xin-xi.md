SELECT

     table\_name       = Case When A.colorder=1 Then D.name Else '' End,

     table\_desc     = Case When A.colorder=1 Then isnull\(F.value,''\) Else '' End,

     colorder   = A.colorder,

     col\_name     = A.name,

     cl\_desc   = isnull\(G.\[value\],''\),

     identity\_       = Case When COLUMNPROPERTY\( A.id,A.name,'IsIdentity'\)=1 Then '1'Else '0' End,

     primary\_key       = Case When exists\(SELECT 1 FROM sysobjects Where xtype='PK' and parent\_obj=A.id and name in \(

                      SELECT name FROM sysindexes WHERE indid in\( SELECT indid FROM sysindexkeys WHERE id = A.id AND colid=A.colid\)\)\) then '1' else '0' end,

     col\_type       = B.name,

     bit\_ = A.Length,

     length       = COLUMNPROPERTY\(A.id,A.name,'PRECISION'\),

     decimal\_   = isnull\(COLUMNPROPERTY\(A.id,A.name,'Scale'\),0\),

     null\_     = Case When A.isnullable=1 Then '1'Else '0' End,

     default\_     = isnull\(E.Text,''\)

 FROM syscolumns A 

 Left Join systypes B On A.xusertype=B.xusertype

 Inner Join sysobjects D On  A.id=D.id  and D.xtype='U' and  D.name&lt;&gt;'dtproperties' 

 Left Join syscomments E on A.cdefault=E.id

 Left Join sys.extended\_properties  G on A.id=G.major\_id and A.colid=G.minor\_id

 Left Join  sys.extended\_properties F On D.id=F.major\_id and F.minor\_id=0

 where d.name='table\_name'    --如果只查询指定表,加上此条件

 Order By

     A.id,A.colorder

