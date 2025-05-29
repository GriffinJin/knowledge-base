# EnumValueIdHandler

交易代码中使用到的枚举的 list_val_id 从拼音替换成英文。

## 前置准备

1. 枚举 Id - 拼音 - 英文 的映射文件，即《枚举拼音治理清单》；

   ![image-20250529102903490](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250529102903490.png)

2. 正则表达式和替换清单；

   JS文件的正则匹配逻辑，以及替换逻辑。

   ![image-20250529103018323](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250529103018323.png)

## 工具执行逻辑

1. 扫描 JS 中 getListFieldValueId 和 setListFieldValueId 中的使用案例，导出。（前置准备代码，准备完成正式执行的时候不需要执行）

   直接扫描项目代码中所有的 js 文件，找到所有的和 listValueId 相关的 JS 代码，生成结果文件《JS文件中枚举Id使用.xlsx》

   判断逻辑代码如下：

   ```java
   for (String line : FileUtil.readUtf8Lines(jsFilepath)) {
       lineNumber++;
       String trimLine = line.trim();
       if (trimLine.startsWith("//")) {
           continue;
       }
       if ((trimLine.contains("getListFieldValueId") && (trimLine.contains("bSame") || trimLine.contains("equalsTo")))
               || trimLine.contains("setListFieldValueId")) {
           ...
       }
   }
   ```

   

2. 根据 《JS文件中枚举Id使用.xlsx》中的结果，梳理需要匹配的正则表达式以及替换表达式，制成文件《regex.xlsx》。

3. 从前置准备文件中获取需要修改的下拉列表 valueId 修改数据。并生成数据库 upate 语句，成品文件为 《udpate.sql》

   ```sql
   Update tapp_list_data tld set tld.list_val_id = '%s' where tld.list_tp_id = '%s' and tld.list_val_id = '%s';
   ```

4. 扫描出交易代码的窗体文件中，下拉列表类型的字段并且使用了枚举 valueId 的属性，替换成正确属性。具体包括：listchildid, defaultvalueid 和 listhiddenitem。

   **defaultvalueid**

   ```xml
   <field dictname="AcDict_A_db_manu_acctg_prms" englishtext="debit manual accounting permission" databind="db_manu_acctg_prms" text="借方手工记账许可" gridwidth="5"  bootstraplayoutheight="1" gridheight="1" item2show="0" hidden="1" defaultvalue="6" defaultvalueid="BUKZ"  listid="E_MANU_ACCTG_PRMS" inputtype="M" id="db_manu_acctg_prms" type="list"  height="30.0" name="借方手工记账许可" gridx="9" gridy="4"/>  
   ```

   **listchildid**

   ```xml
   <field dictname="AcDict_A_bal_charic" inputtype="O" listchildid="JF,DF,ZC"  englishtext="balance characteristic" nullchildflag="1" databind="bal_charic" text="余额性质" gridwidth="5"  bootstraplayoutheight="1" gridheight="1" item2show="0" listhiddenitem="SF"  id="bal_charic" type="list" height="30.0"  name="余额性质"  gridx="9" gridy="1"/>  
   ```

   **listhiddenitem**

   ```xml
   <field databind="busi_oprn_mode" combin="false" dictname="PbDict_A_busi_oprn_mode" inputtype="M" listhiddenitem="XG,FH,SC" text="操作标志" englishtext="business operation mode" hidden="0" gridwidth="5" bootstraplayoutheight="1" gridheight="1" span="" fillx="false" filly="false" alignx="4" aligny="4" autoline="" isminheight="true" outleft="" outtop="" outright="" outbottom="" item2show="2" id="busi_oprn_mode" type="list" width="" height="30.0" name="操作标志" gridx="2" gridy="0"/>
   ```

   *注意：这里替换 listchildid 的时候有坑，比如“支付”和“未支付”，拼音为 ZF 和 WZF，需要替换为 “paid” 和 “unpaid”，在替换 “ZF” 的同时会把 “WZF” 替换成 “Wpaid”，再替换 WZF 就会找不到，所以不能直接 replaceAll，需要通过逗号分隔后完全匹配，单独替换，再拼接写回 listchildid。*

5. 扫描并处理 JS 代码中使用到枚举 valueId 的代码并替换。

   和枚举 ValueId 相关的方法有两个，为 getListFieldValueId 和 setListFieldValueId。这两个方法的参数都包含窗体文件中的 fieldId。所以需要工具可以通过 fieldId 判断出这个字段所使用的具体枚举。

   流程如下：

   1. 在步骤4中，会遍历处理窗体文件，而 JS 文件一般情况下是放在窗体文件同级别的目录下，所以会在处理完窗体文件之后，获取窗体文件的父文件目录进行遍历，处理其中的 js 文件。同时，在处理窗体的时候，需要记录 FieldId 和枚举的映射关系（filedEnumMapper）。

      ```java
      FileUtil.listFolder(PROJECT, new CxFileFilterAdapter() {
          @Override
          public void display(String parent, String name) {
              Map<String, String> filedEnumMapper = new HashMap<>();
              final String[] filePath = {FilenameUtil.concat(parent, name)};
              if (filePath[0].endsWith(".form.cfg")) {
                  String path = FilenameUtil.concat(parent, name);
                  CxNode formNode = XmlParserUtil.unpack(FileUtil.readFileToString(path));
                  XmlParserUtil.listNode(formNode, new CxNodeFilter() {
                      @Override
                      public boolean display(CxNode parent, CxNode node) {
                          if (node.getName().equals("field")) {
                              String id = node.getAttributes().get("id");
                              String fieldType = node.getAttributes().get("type");
                              if (useEnumFileTypeList.contains(fieldType)) {
                                  String listTPId = node.getAttributes().get("listid");
                                  if (StrUtil.isNotBlank(listTPId)) {
                                      filedEnumMapper.put(id, listTPId);
                                  }
                      }
                  });
                  FileUtil.write(filePath[0],XmlParserUtil.pack(formNode),false);
                  // 扫描当前窗体文件下的所有的 JS 文件
                  FileUtil.listFolder(txn_path, new CxFileFilterAdapter() {
                      @Override
                      public void display(String parent, String name) {
                          String filepath = Paths.get(parent).resolve(name).toString();
                          if (filepath.endsWith(".js")) {
                              scanJsModification(filepath,filedEnumMapper);
                          }
                      }
                  });
              }
          }
      });
      ```

   2. 按行遍历 js 文件，并逐个匹配 regex.xlsx 中的正则表达式。如果可以正确匹配，则会用 list_val_id 和 fied_id 所对应的 list_tp_id 在 枚举拼音治理清单 中寻找对应的可替换 list_val_id，并记录 JsCodeModification。

   3. JsCodeModification 为单个 js 文件替换过程中的核心文件，JsCodeModifications 为 JsCodeModification 合集，可以 js 文件替换过程的上下文，记录所有 js 文件的替换过程和状态。

   4. 根据 JsCodeModifications 生成执行结果文件”JS文件更新记录.xlsx“。

## 预期效果

### 窗体文件修改

![image-20250528173357765](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250528173357765.png)

![image-20250528173533636](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250528173533636.png)

![image-20250528173832775](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250528173832775.png)

### 数据库更新脚本

![image-20250529105921674](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250529105921674.png)

### javascript 文件修改

![image-20250529110021868](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250529110021868.png)

### 执行结果文件

![image-20250529110128818](https://gcore.jsdelivr.net/gh/GriffinJin/image-host@main/image/image-20250529110128818.png)

