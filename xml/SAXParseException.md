
问题原因：

xml文件格式问题，常见格式错误格式为UTF-8-BOM

解决方案：

将文件格式的BOM格式去掉，转换为UTF8格式


```
[org.xml.sax.SAXParseException; lineNumber: 1; columnNumber: 1; 前言中不允许有内容。]
	at javax.xml.bind.helpers.AbstractUnmarshallerImpl.createUnmarshalException(AbstractUnmarshallerImpl.java:335)
	at com.sun.xml.internal.bind.v2.runtime.unmarshaller.UnmarshallerImpl.createUnmarshalException(UnmarshallerImpl.java:563)
	at com.sun.xml.internal.bind.v2.runtime.unmarshaller.UnmarshallerImpl.unmarshal0(UnmarshallerImpl.java:249)
	at com.sun.xml.internal.bind.v2.runtime.unmarshaller.UnmarshallerImpl.unmarshal(UnmarshallerImpl.java:214)
	at javax.xml.bind.helpers.AbstractUnmarshallerImpl.unmarshal(AbstractUnmarshallerImpl.java:157)
	at javax.xml.bind.helpers.AbstractUnmarshallerImpl.unmarshal(AbstractUnmarshallerImpl.java:214)
	at com.starnetuc.svms.util.XMLUtils.convertXmlFileToObject(XMLUtils.java:68)
	at com.starnetuc.svms.util.XMLUtils.convertXmlFileToObject(XMLUtils.java:77)
	at com.starnetuc.svms.service.upgrade.FileTest.main(FileTest.java:23)
Caused by: org.xml.sax.SAXParseException; lineNumber: 1; columnNumber: 1; 前言中不允许有内容。
	at com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.createSAXParseException(ErrorHandlerWrapper.java:203)
	at com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.fatalError(ErrorHandlerWrapper.java:177)
	at com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:400)
	at com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:327)
	at com.sun.org.apache.xerces.internal.impl.XMLScanner.reportFatalError(XMLScanner.java:1472)
	at com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl$PrologDriver.next(XMLDocumentScannerImpl.java:994)
	at com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl.next(XMLDocumentScannerImpl.java:602)
	at com.sun.org.apache.xerces.internal.impl.XMLNSDocumentScannerImpl.next(XMLNSDocumentScannerImpl.java:112)
	at com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl.scanDocument(XMLDocumentFragmentScannerImpl.java:505)
	at com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:841)
	at com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:770)
	at com.sun.org.apache.xerces.internal.parsers.XMLParser.parse(XMLParser.java:141)
	at com.sun.org.apache.xerces.internal.parsers.AbstractSAXParser.parse(AbstractSAXParser.java:1213)
	at com.sun.org.apache.xerces.internal.jaxp.SAXParserImpl$JAXPSAXParser.parse(SAXParserImpl.java:643)
	at com.sun.xml.internal.bind.v2.runtime.unmarshaller.UnmarshallerImpl.unmarshal0(UnmarshallerImpl.java:243)
	... 6 more

```
