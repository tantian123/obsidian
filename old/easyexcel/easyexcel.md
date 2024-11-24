1.源码分析
读取
```
String filePath = ExcelReadDemo.class.getResource("/test.xlsx").getPath();  
  
// 创建ExcelReader对象，指定文件名和监听器  
ExcelReader excelReader = EasyExcel.read(filePath, DemoData.class, new DemoDataListener()).build();  
  
// 读取Sheet  
ReadSheet readSheet = EasyExcel.readSheet(0).build();  
excelReader.read(readSheet);  
  
// 关闭资源  
excelReader.finish();
```

1.read方法
返回ExcelReaderBuilder
excelReaderBuilder.head().registerReadListener();
建造者模式 分别构造reader的读取的类和监听器

核心是readWorkbook 下一步会用到

2.build方法
返回ExcelReader 
new ExcelReader(this.readWorkbook) 返回一个ReadWorkbook类对象

3. 调用ExcelReader.read方法
EasyExcel.readSheet(0).build()  构造ReadSheet类即读取操作
readSheet作为read方法的参数 即构造读取的参数

this.excelAnalyser.analysis

``` java
public void analysis(List<ReadSheet> readSheetList, Boolean readAll) {  
    try {  
        if (!readAll && CollectionUtils.isEmpty(readSheetList)) {  
            throw new IllegalArgumentException("Specify at least one read sheet.");  
        } else {  
            this.analysisContext.readWorkbookHolder().setParameterSheetDataList(readSheetList);  
            this.analysisContext.readWorkbookHolder().setReadAll(readAll);  
  
            try {  
                this.excelReadExecutor.execute();  
            } catch (ExcelAnalysisStopException var4) {  
                if (LOGGER.isDebugEnabled()) {  
                    LOGGER.debug("Custom stop!");  
                }  
            }  
  
        }  
    } catch (RuntimeException var5) {  
        this.finish();  
        throw var5;  
    } catch (Throwable var6) {  
        this.finish();  
        throw new ExcelAnalysisException(var6);  
    }  
}
```

解析操作excelReadExecutor.execute() 执行Excel文件的解析操作
``` java
迭代遍历Sheet列表
匹配Sheet对象
parseXmlSource()

public void execute() {  
    Iterator var1 = this.sheetList.iterator();  
  
    while(var1.hasNext()) {  
        ReadSheet readSheet = (ReadSheet)var1.next();  
        readSheet = SheetUtils.match(readSheet, this.xlsxReadContext);  
        if (readSheet != null) {  
            this.xlsxReadContext.currentSheet(readSheet);  
            this.parseXmlSource((InputStream)this.sheetMap.get(readSheet.getSheetNo()), new XlsxRowHandler(this.xlsxReadContext));  
            this.readComments(readSheet);  
            this.xlsxReadContext.analysisEventProcessor().endSheet(this.xlsxReadContext);  
        }  
    }  
  
}
```

``` java
创建SAX解析器工厂 配置SAX解析器和XMLReader
设置ContentHandler和解析 将指定的`ContentHandler`对象设置为XMLReader的内容处理器，并调用xmlReader.parse(inputSource)方法开始解析XML源数据
xmlReader.parse的底层 不用了解了

private void parseXmlSource(InputStream inputStream, ContentHandler handler) {  
    InputSource inputSource = new InputSource(inputStream);  
  
    try {  
        String xlsxSAXParserFactoryName = this.xlsxReadContext.xlsxReadWorkbookHolder().getSaxParserFactoryName();  
        SAXParserFactory saxFactory;  
        if (StringUtils.isEmpty(xlsxSAXParserFactoryName)) {  
            saxFactory = SAXParserFactory.newInstance();  
        } else {  
            saxFactory = SAXParserFactory.newInstance(xlsxSAXParserFactoryName, (ClassLoader)null);  
        }  
  
        try {  
            saxFactory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);  
        } catch (Throwable var20) {  
        }  
  
        try {  
            saxFactory.setFeature("http://xml.org/sax/features/external-general-entities", false);  
        } catch (Throwable var19) {  
        }  
  
        try {  
            saxFactory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);  
        } catch (Throwable var18) {  
        }  
  
        SAXParser saxParser = saxFactory.newSAXParser();  
        XMLReader xmlReader = saxParser.getXMLReader();  
        xmlReader.setContentHandler(handler);  
        xmlReader.parse(inputSource);  
        inputStream.close();  
    } catch (ParserConfigurationException | SAXException | IOException var21) {  
        throw new ExcelAnalysisException(var21);  
    } finally {  
        if (inputStream != null) {  
            try {  
                inputStream.close();  
            } catch (IOException var17) {  
                throw new ExcelAnalysisException("Can not close 'inputStream'!");  
            }  
        }  
  
    }  
  
}
```


4.excelReader.finish()
调用ExcelAnalyser的finish方法
即ExcelAnalyserImpl类的finish方法（`在读取Excel文件完成后执行一系列清理操作，包括关闭资源、清除缓存和处理异常`）




写入
```
String filePath = filePath="C:\\Users\\tantian\\Desktop\\writeTest.xlsx";  
// 创建excelWriter  
ExcelWriter excelWriter = EasyExcel.write(filePath, DemoData.class).build();  
  
// 写入Sheet  
WriteSheet writeSheet = EasyExcel.writerSheet("模板").build();  
excelWriter.write(data(),writeSheet);  
  
// 关闭资源  
excelWriter.finish();
```

```
public ExcelWriter write(Collection<?> data, WriteSheet writeSheet, WriteTable writeTable) {  
    this.excelBuilder.addContent(data, writeSheet, writeTable);  
    return this;}

public void addContent(Collection<?> data, WriteSheet writeSheet, WriteTable writeTable) {  
    try {  
        this.context.currentSheet(writeSheet, WriteTypeEnum.ADD);  
        this.context.currentTable(writeTable);  
        if (this.excelWriteAddExecutor == null) {  
            this.excelWriteAddExecutor = new ExcelWriteAddExecutor(this.context);  
        }  
  
        this.excelWriteAddExecutor.add(data);  
    } catch (RuntimeException var5) {  
        this.finishOnException();  
        throw var5;  
    } catch (Throwable var6) {  
        this.finishOnException();  
        throw new ExcelGenerateException(var6);  
    }  
}
```
this.excelWriteAddExecutor.add(data);
``` java
获取写入上下文和相关索引
循环遍历数据集合 oneRowData
计算行索引
将一行数据添加到Excel中  addOneRowOfDataToExcel

public void add(Collection<?> data) {  
    if (CollectionUtils.isEmpty((Collection)data)) {  
        data = new ArrayList();  
    }  
  
    WriteSheetHolder writeSheetHolder = this.writeContext.writeSheetHolder();  
    int newRowIndex = writeSheetHolder.getNewRowIndexAndStartDoWrite();  
    if (writeSheetHolder.isNew() && !writeSheetHolder.getExcelWriteHeadProperty().hasHead()) {  
        newRowIndex += this.writeContext.currentWriteHolder().relativeHeadRowIndex();  
    }  
  
    int relativeRowIndex = 0;  
  
    for(Iterator var5 = ((Collection)data).iterator(); var5.hasNext(); ++relativeRowIndex) {  
        Object oneRowData = var5.next();  
        int lastRowIndex = relativeRowIndex + newRowIndex;  
        this.addOneRowOfDataToExcel(oneRowData, lastRowIndex, relativeRowIndex);  
    }  
  
}
```