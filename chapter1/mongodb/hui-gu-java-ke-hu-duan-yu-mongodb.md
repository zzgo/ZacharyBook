### 原生java驱动

```
MongoClient -> MongoDatabase -> MongoCollection

MongoClient被设计成线程安全、可以被多线程共享的。通常访问数据库集群的应用只需要一个实例
如果需要使用pojo对象读写，需要将PojoCodecProvider注入到client
```

查询和更新的API类

```
查询器：com.mongodb.client.model.Filters
更新器：com.mongodb.client.model.Updates
投影器：com.mongodb.client.model.Projections
```

Spring mongodb解析

xml配置

```
	<!-- mongodb连接池配置 -->
	<mongo:mongo-client host="192.168.111.128" port="27022">
		<mongo:client-options 
		     write-concern="ACKNOWLEDGED"
		      connections-per-host="100"
		      threads-allowed-to-block-for-connection-multiplier="5"
		      max-wait-time="120000"
			  connect-timeout="10000"/> 
	</mongo:mongo-client>
	
	<!-- mongodb数据库工厂配置 -->
	<mongo:db-factory dbname="lison" mongo-ref="mongo" />
	
	<!-- mongodb数据转换器配置，支持BigDecimal -->
  	<mongo:mapping-converter base-package="com.zachary.entity">
	  <mongo:custom-converters>
	      <mongo:converter>
	        <bean class="com.zachary.convert.BigDecimalToDecimal128Converter"/>
	      </mongo:converter>
	      <mongo:converter>
	        <bean class="com.zachary.convert.Decimal128ToBigDecimalConverter"/>
	      </mongo:converter>
    	  </mongo:custom-converters>
	</mongo:mapping-converter>

    	<!-- mongodb模板配置 -->
	<bean id="anotherMongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">
		<constructor-arg name="mongoDbFactory" ref="mongoDbFactory" />
 		<constructor-arg name="mongoConverter" ref="mappingConverter"/>
		<property name="writeResultChecking" value="EXCEPTION"></property>
	</bean>
```

开发

```
模板模式，基于MongoOperations进行操作，基于pojo的操作，配合@document(collection="users")注解开发；
查询和更新的API类
查询器：org.springframework.data.mongodb.core.query.Query
查询条件：org.springframework.data.mongodb.core.query.Criteria
更新器：org.springframework.data.mongodb.core.query.Update

```



