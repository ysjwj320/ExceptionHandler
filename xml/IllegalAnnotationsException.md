
问题原因：

在创建xml解析类的时候，即有xmlElement注解，又有get方法，解析时，不知道选用哪种方式获取xml属性

解决方法：

1、在类上添加注解，@XmlAccessorType(XmlAccessType.FIELD)

2、将xmlElmenent注解放在属性的get方法上

```
类的两个属性具有相同名称 "fileName"
	this problem is related to the following location:
		at public java.lang.String com.starnetuc.svms.uda.entity.UpgradeVersion$Support.getFileName()
		at com.starnetuc.svms.uda.entity.UpgradeVersion$Support
		at public com.starnetuc.svms.uda.entity.UpgradeVersion$Support com.starnetuc.svms.uda.entity.UpgradeVersion.getSupport()
		at com.starnetuc.svms.uda.entity.UpgradeVersion
	this problem is related to the following location:
		at private java.lang.String com.starnetuc.svms.uda.entity.UpgradeVersion$Support.fileName
		at com.starnetuc.svms.uda.entity.UpgradeVersion$Support
		at public com.starnetuc.svms.uda.entity.UpgradeVersion$Support com.starnetuc.svms.uda.entity.UpgradeVersion.getSupport()
		at com.starnetuc.svms.uda.entity.UpgradeVersion

	at com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException$Builder.check(IllegalAnnotationsException.java:91)
	at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.getTypeInfoSet(JAXBContextImpl.java:445)
	at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.<init>(JAXBContextImpl.java:277)
	at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.<init>(JAXBContextImpl.java:124)
	at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl$JAXBContextBuilder.build(JAXBContextImpl.java:1123)
	at com.sun.xml.internal.bind.v2.ContextFactory.createContext(ContextFactory.java:147)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at javax.xml.bind.ContextFinder.newInstance(ContextFinder.java:247)
	at javax.xml.bind.ContextFinder.newInstance(ContextFinder.java:234)
	at javax.xml.bind.ContextFinder.find(ContextFinder.java:462)
	at javax.xml.bind.JAXBContext.newInstance(JAXBContext.java:641)
	at javax.xml.bind.JAXBContext.newInstance(JAXBContext.java:584)
	at com.starnetuc.svms.util.XMLUtils.newContext(XMLUtils.java:109)
	... 3 more
```
