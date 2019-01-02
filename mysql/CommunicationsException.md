```
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure

The last packet successfully received from the server was 100,026 milliseconds ago.  The last packet sent successfully to the server was 22 milliseconds ago.
	at com.mysql.cj.jdbc.exceptions.SQLError.createCommunicationsException(SQLError.java:590)
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:57)
	at com.mysql.cj.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:1983)
	at com.mysql.cj.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:1826)
	at com.mysql.cj.jdbc.PreparedStatement.executeQuery(PreparedStatement.java:1923)
	at com.mchange.v2.c3p0.impl.NewProxyPreparedStatement.executeQuery(NewProxyPreparedStatement.java:353)
	at cn.starnet.bmu.mysql.MysqlConnectionTest.main(MysqlConnectionTest.java:58)
Caused by: com.mysql.cj.core.exceptions.CJCommunicationsException: Communications link failure

The last packet successfully received from the server was 100,026 milliseconds ago.  The last packet sent successfully to the server was 22 milliseconds ago.
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:54)
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:93)
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:133)
	at com.mysql.cj.core.exceptions.ExceptionFactory.createCommunicationsException(ExceptionFactory.java:149)
	at com.mysql.cj.mysqla.io.MysqlaProtocol.readPacket(MysqlaProtocol.java:527)
	at com.mysql.cj.mysqla.io.MysqlaProtocol.checkErrorPacket(MysqlaProtocol.java:723)
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sendCommand(MysqlaProtocol.java:662)
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sqlQueryDirect(MysqlaProtocol.java:950)
	at com.mysql.cj.mysqla.MysqlaSession.sqlQueryDirect(MysqlaSession.java:431)
	at com.mysql.cj.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:1978)
	... 4 more
Caused by: java.io.EOFException: Can not read response from server. Expected to read 4 bytes, read 0 bytes before connection was unexpectedly lost.
	at com.mysql.cj.core.io.FullReadInputStream.readFully(FullReadInputStream.java:61)
	at com.mysql.cj.mysqla.io.SimplePacketReader.readHeader(SimplePacketReader.java:60)
	at com.mysql.cj.mysqla.io.TimeTrackingPacketReader.readHeader(TimeTrackingPacketReader.java:48)
	at com.mysql.cj.mysqla.io.MultiPacketReader.readHeader(MultiPacketReader.java:51)
	at com.mysql.cj.mysqla.io.MysqlaProtocol.readPacket(MysqlaProtocol.java:521)
	... 9 more
  ```
  
  情景：使用c3p0连接池，当服务器运行时间大于数据库的interactive_timeout和wait_timeout（默认8小时）后，出现改异常。
  
  其中，异常信息为中 100,026 表示该连接在100秒前，最后一次正常连接。
  
  问题原因：使用了半断开的connection（此处是该连接的Server端已经关闭），断开的原因有可能是一下几点
  
  * 数据库的interactive_timeout和wait_timeout小于数据库连接池的maxIdleTime
  
  * 没有配置maxIdleTime（即默认值0），当c3p0没有配置maxIdleTime=0时，数据库连接是永久有效的。
  
  下面是使用hibernate后，抛出的异常信息
  
  ```
  Caused by: com.mysql.cj.core.exceptions.CJCommunicationsException: The last packet successfully received from the server was 291,807,903 milliseconds ago.  The last packet sent successfully to the server was 291,807,904 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method) ~[?:1.8.0_151]
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62) ~[?:1.8.0_151]
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45) ~[?:1.8.0_151]
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423) ~[?:1.8.0_151]
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:54) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:93) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.core.exceptions.ExceptionFactory.createException(ExceptionFactory.java:133) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.core.exceptions.ExceptionFactory.createCommunicationsException(ExceptionFactory.java:149) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.send(MysqlaProtocol.java:561) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sendCommand(MysqlaProtocol.java:644) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sqlQueryDirect(MysqlaProtocol.java:950) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.MysqlaSession.sqlQueryDirect(MysqlaSession.java:431) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:1978) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:1826) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.PreparedStatement.executeQuery(PreparedStatement.java:1923) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mchange.v2.c3p0.impl.NewProxyPreparedStatement.executeQuery(NewProxyPreparedStatement.java:353) ~[c3p0-0.9.5.2.jar:0.9.5.2]
	at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:60) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.getResultSet(Loader.java:2168) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1931) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1893) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQuery(Loader.java:938) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:341) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:311) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.loadEntity(Loader.java:2282) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.entity.AbstractEntityLoader.load(AbstractEntityLoader.java:61) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.entity.EntityLoader.loadByUniqueKey(EntityLoader.java:144) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.persister.entity.AbstractEntityPersister.loadByUniqueKey(AbstractEntityPersister.java:2228) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.type.EntityType.loadByUniqueKey(EntityType.java:699) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.type.EntityType.resolve(EntityType.java:434) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.TwoPhaseLoad.doInitializeEntity(TwoPhaseLoad.java:165) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.TwoPhaseLoad.initializeEntity(TwoPhaseLoad.java:125) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.AbstractRowReader.performTwoPhaseLoad(AbstractRowReader.java:238) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.AbstractRowReader.finishUp(AbstractRowReader.java:209) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.ResultSetProcessorImpl.extractResults(ResultSetProcessorImpl.java:133) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeLoad(AbstractLoadPlanBasedLoader.java:122) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeLoad(AbstractLoadPlanBasedLoader.java:86) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.collection.plan.AbstractLoadPlanBasedCollectionInitializer.initialize(AbstractLoadPlanBasedCollectionInitializer.java:87) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.persister.collection.AbstractCollectionPersister.initialize(AbstractCollectionPersister.java:688) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.event.internal.DefaultInitializeCollectionEventListener.onInitializeCollection(DefaultInitializeCollectionEventListener.java:75) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.internal.SessionImpl.initializeCollection(SessionImpl.java:2185) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection$4.doWork(AbstractPersistentCollection.java:565) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.withTemporarySessionIfNeeded(AbstractPersistentCollection.java:247) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.initialize(AbstractPersistentCollection.java:561) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.forceInitialization(AbstractPersistentCollection.java:729) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.StatefulPersistenceContext.initializeNonLazyCollections(StatefulPersistenceContext.java:926) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:346) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2692) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2675) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2507) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.list(Loader.java:2502) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.hql.QueryLoader.list(QueryLoader.java:502) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.hql.internal.ast.QueryTranslatorImpl.list(QueryTranslatorImpl.java:384) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.query.spi.HQLQueryPlan.performList(HQLQueryPlan.java:216) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.internal.SessionImpl.list(SessionImpl.java:1490) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.doList(AbstractProducedQuery.java:1445) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.list(AbstractProducedQuery.java:1414) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.Query.getResultList(Query.java:146) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.criteria.internal.compile.CriteriaQueryTypeQueryAdapter.getResultList(CriteriaQueryTypeQueryAdapter.java:72) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at cn.starnet.bmu.common.BaseJpaDao.getByCondition(BaseJpaDao.java:145) ~[classes/:?]
	at cn.starnet.bmu.dao.impl.BusinessDaoImpl.getBusinessesByCondition(BusinessDaoImpl.java:104) ~[classes/:?]
	at sun.reflect.GeneratedMethodAccessor65.invoke(Unknown Source) ~[?:?]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_151]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_151]
	at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:338) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:197) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:294) ~[spring-tx-5.0.0.RELEASE.jar:5.0.0.RELEASE]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:98) ~[spring-tx-5.0.0.RELEASE.jar:5.0.0.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:185) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:212) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at com.sun.proxy.$Proxy52.getBusinessesByCondition(Unknown Source) ~[?:?]
	at cn.starnet.bmu.service.impl.BusinessService.registerBusinessesByOwner(BusinessService.java:385) ~[classes/:?]
	at cn.starnet.bmu.service.impl.RpcService.heartbeat(RpcService.java:233) ~[classes/:?]
	at cn.starnet.bmu.service.impl.RpcService.doAction(RpcService.java:73) ~[classes/:?]
	at cn.starnet.bmu.mq.RpcBusinessServer.onMessage(RpcBusinessServer.java:32) ~[classes/:?]
	at sun.reflect.GeneratedMethodAccessor68.invoke(Unknown Source) ~[?:?]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_151]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_151]
	at org.springframework.util.MethodInvoker.invoke(MethodInvoker.java:280) ~[spring-core-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.amqp.rabbit.listener.adapter.MessageListenerAdapter.invokeListenerMethod(MessageListenerAdapter.java:372) ~[spring-rabbit-2.0.0.RELEASE.jar:2.0.0.RELEASE]
	... 10 more
Caused by: java.net.SocketException: Broken pipe (Write failed)
	at java.net.SocketOutputStream.socketWrite0(Native Method) ~[?:1.8.0_151]
	at java.net.SocketOutputStream.socketWrite(SocketOutputStream.java:111) ~[?:1.8.0_151]
	at java.net.SocketOutputStream.write(SocketOutputStream.java:155) ~[?:1.8.0_151]
	at sun.security.ssl.OutputRecord.writeBuffer(OutputRecord.java:431) ~[?:1.8.0_151]
	at sun.security.ssl.OutputRecord.write(OutputRecord.java:417) ~[?:1.8.0_151]
	at sun.security.ssl.SSLSocketImpl.writeRecordInternal(SSLSocketImpl.java:886) ~[?:1.8.0_151]
	at sun.security.ssl.SSLSocketImpl.writeRecord(SSLSocketImpl.java:857) ~[?:1.8.0_151]
	at sun.security.ssl.AppOutputStream.write(AppOutputStream.java:123) ~[?:1.8.0_151]
	at java.io.BufferedOutputStream.flushBuffer(BufferedOutputStream.java:82) ~[?:1.8.0_151]
	at java.io.BufferedOutputStream.flush(BufferedOutputStream.java:140) ~[?:1.8.0_151]
	at com.mysql.cj.mysqla.io.SimplePacketSender.send(SimplePacketSender.java:50) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.TimeTrackingPacketSender.send(TimeTrackingPacketSender.java:43) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.send(MysqlaProtocol.java:552) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sendCommand(MysqlaProtocol.java:644) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.io.MysqlaProtocol.sqlQueryDirect(MysqlaProtocol.java:950) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.mysqla.MysqlaSession.sqlQueryDirect(MysqlaSession.java:431) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:1978) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:1826) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mysql.cj.jdbc.PreparedStatement.executeQuery(PreparedStatement.java:1923) ~[mysql-connector-java-6.0.6.jar:6.0.6]
	at com.mchange.v2.c3p0.impl.NewProxyPreparedStatement.executeQuery(NewProxyPreparedStatement.java:353) ~[c3p0-0.9.5.2.jar:0.9.5.2]
	at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:60) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.getResultSet(Loader.java:2168) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1931) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.executeQueryStatement(Loader.java:1893) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQuery(Loader.java:938) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:341) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:311) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.loadEntity(Loader.java:2282) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.entity.AbstractEntityLoader.load(AbstractEntityLoader.java:61) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.entity.EntityLoader.loadByUniqueKey(EntityLoader.java:144) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.persister.entity.AbstractEntityPersister.loadByUniqueKey(AbstractEntityPersister.java:2228) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.type.EntityType.loadByUniqueKey(EntityType.java:699) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.type.EntityType.resolve(EntityType.java:434) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.TwoPhaseLoad.doInitializeEntity(TwoPhaseLoad.java:165) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.TwoPhaseLoad.initializeEntity(TwoPhaseLoad.java:125) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.AbstractRowReader.performTwoPhaseLoad(AbstractRowReader.java:238) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.AbstractRowReader.finishUp(AbstractRowReader.java:209) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.process.internal.ResultSetProcessorImpl.extractResults(ResultSetProcessorImpl.java:133) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeLoad(AbstractLoadPlanBasedLoader.java:122) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeLoad(AbstractLoadPlanBasedLoader.java:86) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.collection.plan.AbstractLoadPlanBasedCollectionInitializer.initialize(AbstractLoadPlanBasedCollectionInitializer.java:87) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.persister.collection.AbstractCollectionPersister.initialize(AbstractCollectionPersister.java:688) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.event.internal.DefaultInitializeCollectionEventListener.onInitializeCollection(DefaultInitializeCollectionEventListener.java:75) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.internal.SessionImpl.initializeCollection(SessionImpl.java:2185) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection$4.doWork(AbstractPersistentCollection.java:565) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.withTemporarySessionIfNeeded(AbstractPersistentCollection.java:247) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.initialize(AbstractPersistentCollection.java:561) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.collection.internal.AbstractPersistentCollection.forceInitialization(AbstractPersistentCollection.java:729) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.internal.StatefulPersistenceContext.initializeNonLazyCollections(StatefulPersistenceContext.java:926) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doQueryAndInitializeNonLazyCollections(Loader.java:346) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2692) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.doList(Loader.java:2675) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2507) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.Loader.list(Loader.java:2502) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.loader.hql.QueryLoader.list(QueryLoader.java:502) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.hql.internal.ast.QueryTranslatorImpl.list(QueryTranslatorImpl.java:384) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.engine.query.spi.HQLQueryPlan.performList(HQLQueryPlan.java:216) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.internal.SessionImpl.list(SessionImpl.java:1490) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.doList(AbstractProducedQuery.java:1445) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.internal.AbstractProducedQuery.list(AbstractProducedQuery.java:1414) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.Query.getResultList(Query.java:146) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at org.hibernate.query.criteria.internal.compile.CriteriaQueryTypeQueryAdapter.getResultList(CriteriaQueryTypeQueryAdapter.java:72) ~[hibernate-core-5.2.12.Final.jar:5.2.12.Final]
	at cn.starnet.bmu.common.BaseJpaDao.getByCondition(BaseJpaDao.java:145) ~[classes/:?]
	at cn.starnet.bmu.dao.impl.BusinessDaoImpl.getBusinessesByCondition(BusinessDaoImpl.java:104) ~[classes/:?]
	at sun.reflect.GeneratedMethodAccessor65.invoke(Unknown Source) ~[?:?]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_151]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_151]
	at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:338) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:197) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:294) ~[spring-tx-5.0.0.RELEASE.jar:5.0.0.RELEASE]
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:98) ~[spring-tx-5.0.0.RELEASE.jar:5.0.0.RELEASE]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:185) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:212) ~[spring-aop-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at com.sun.proxy.$Proxy52.getBusinessesByCondition(Unknown Source) ~[?:?]
	at cn.starnet.bmu.service.impl.BusinessService.registerBusinessesByOwner(BusinessService.java:385) ~[classes/:?]
	at cn.starnet.bmu.service.impl.RpcService.heartbeat(RpcService.java:233) ~[classes/:?]
	at cn.starnet.bmu.service.impl.RpcService.doAction(RpcService.java:73) ~[classes/:?]
	at cn.starnet.bmu.mq.RpcBusinessServer.onMessage(RpcBusinessServer.java:32) ~[classes/:?]
	at sun.reflect.GeneratedMethodAccessor68.invoke(Unknown Source) ~[?:?]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_151]
	at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_151]
	at org.springframework.util.MethodInvoker.invoke(MethodInvoker.java:280) ~[spring-core-5.0.2.RELEASE.jar:5.0.2.RELEASE]
	at org.springframework.amqp.rabbit.listener.adapter.MessageListenerAdapter.invokeListenerMethod(MessageListenerAdapter.java:372) ~[spring-rabbit-2.0.0.RELEASE.jar:2.0.0.RELEASE]
	... 10 more
  ```
  
  
