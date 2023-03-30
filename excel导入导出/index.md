# Excel导入导出


## Excel导入

### easyexcel导入(无实体类)

1. 定义一个实体(文件读取监听器)

```java
import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class DynamicEasyExcelListener extends AnalysisEventListener<Map<Integer, String>> {
    /**
     * 表头数据（存储所有的表头数据）
     */
    private List<Map<Integer, String>> headList = new ArrayList<>();
    /**
     * 数据体
     */
    private List<Map<Integer, String>> dataList = new ArrayList<>();

    /**
     * 这里会一行行的返回头
     *
     * @param headMap
     * @param context
     */
    @Override
    public void invokeHeadMap(Map<Integer, String> headMap, AnalysisContext context) {
        //存储全部表头数据
        headList.add(headMap);
    }

    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data    one row value. Is is same as {@link AnalysisContext#readRowHolder()}
     * @param context
     */
    @Override
    public void invoke(Map<Integer, String> data, AnalysisContext context) {
        dataList.add(data);
    }

    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
    }

    public List<Map<Integer, String>> getHeadList() {
        return headList;
    }

    public List<Map<Integer, String>> getDataList() {
        return dataList;
    }
}
```

2. 导入工具类

```java
import com.alibaba.excel.EasyExcelFactory;
import org.apache.commons.compress.utils.Lists;

import java.io.ByteArrayInputStream;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;

public class DynamicEasyExcelImportUtils {
    /**
     * 动态获取全部列和数据体
     *
     * @param stream         excel文件流
     * @param parseRowNumber 指定读取行
     * @return
     */
    public static DynamicEasyExcelListener parseExcelToView(byte[] stream, Integer parseRowNumber) {
        DynamicEasyExcelListener readListener = new DynamicEasyExcelListener();
        EasyExcelFactory.read(new ByteArrayInputStream(stream)).registerReadListener(readListener).headRowNumber(parseRowNumber).sheet(0).doRead();
        return readListener;
    }

    public static List<Map<String, String>> packDataBody(List<Map<Integer, String>> headList, List<Map<Integer, String>> dataList) {
        //获取头部,取最后一次解析的列头数据
        Map<Integer, String> excelHeadIdxNameMap = headList.get(headList.size() - 1);
        //封装数据体
        List<Map<String, String>> excelDataList = Lists.newArrayList();
        for (Map<Integer, String> dataRow : dataList) {
            Map<String, String> rowData = new LinkedHashMap<>();
            excelHeadIdxNameMap.entrySet().forEach(columnHead -> {
                rowData.put(columnHead.getValue(), dataRow.get(columnHead.getKey()));
            });
            excelDataList.add(rowData);
        }
        return excelDataList;
    }
}
```

3. 使用教程

```java
// 获取文件信息 MultipartFile file
InputStream inputStream = file.getInputStream();
byte[] stream = IoUtils.toByteArray(inputStream);
DynamicEasyExcelListener readListener = parseExcelToView(stream, 1);
// 获取表头
List<Map<Integer, String>> headList = readListener.getHeadList();
if (CollectionUtils.isEmpty(headList)) {
    throw new RuntimeException("Excel未包含表头");
}
```

```java
// 获取表数据
List<Map<Integer, String>> dataList = readListener.getDataList();
if (CollectionUtils.isEmpty(dataList)) {
    throw new RuntimeException("Excel未包含数据");
}
```

```java
//封装数据体
List<Map<String, String>> excelDataList = packDataBody(headList, dataList);
```
* excelDataList将会得到动态的list,然后进一步处理添加

## Excel导出

### easyexcel导出(无实体类)

[easyexcel导出-参考文档](https://blog.csdn.net/gaoler_123_lic/article/details/123003708)

1. pom.xml引入

```xml
<!-- 阿里JSON解析器 -->
<dependency>　　　　　　
    <groupId>com.alibaba</groupId>　　　　　　
    <artifactId>fastjson</artifactId>　　　　　　
    <version>1.2.66</version>　 　
</dependency>
```

```xml
<!--阿里巴巴导出 easyexcel jar-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>2.2.6</version>
</dependency>
```

```xml

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.24</version>
</dependency>
```

2. 定义两个实体类

```java
import lombok.Data;

import java.io.Serializable;
import java.util.List;
import java.util.Map;

@Data
public class NoModelWriteData implements Serializable {
    //文件名
    private String fileName;
    //表头数组
    private List<String> headMap;
    //对应数据字段数组
    private List<String> dataStrMap;
    //数据集合
    private List<Map<String, Object>> dataList;
}
```

```java
import lombok.Data;

import java.io.Serializable;
import java.util.List;

@Data
public class SimpleWriteData implements Serializable {
    //文件名
    private String fileName;
    //数据列表
    private List<?> dataList;
}
```

3. 添加工具类内容

```java
import com.alibaba.excel.EasyExcel;
import com.alibaba.fastjson.JSON;
import org.springframework.web.bind.annotation.RequestBody;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLEncoder;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class EasyExcelUtils {
    //不创建对象的导出
    public void noModleWrite(@RequestBody NoModelWriteData data, HttpServletResponse response) throws IOException {

        try {
//            response.setContentType("application/vnd.ms-excel");
            response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
            response.setCharacterEncoding("utf-8");
            // 这里URLEncoder.encode可以防止中文乱码 当然和easyexcel没有关系
            String fileName = URLEncoder.encode(data.getFileName(), "UTF-8");
            response.setHeader("Content-disposition", "attachment;filename=" + fileName + ".xlsx");
            // 这里需要设置不关闭流
            EasyExcel.write(response.getOutputStream())
                    .head(head(data.getHeadMap()))
                    .sheet(data.getFileName())
                    .registerWriteHandler(new ExcelWidthStyleStrategy())
                    .doWrite(dataList(data.getDataList()
                            , data.getDataStrMap()));
        } catch (Exception e) {
            // 重置response
            response.reset();
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            Map<String, String> map = new HashMap<String, String>();
            map.put("status", "failure");
            map.put("message", "下载文件失败" + e.getMessage());
            response.getWriter().println(JSON.toJSONString(map));
        }
    }

    //设置表头
    private List<List<String>> head(List<String> headMap) {
        List<List<String>> list = new ArrayList<List<String>>();

        for (String head : headMap) {
            List<String> headList = new ArrayList<String>();
            headList.add(head);
            list.add(headList);
        }
        return list;
    }

    //设置导出的数据内容
    private List<List<Object>> dataList(List<Map<String, Object>> dataList, List<String> dataStrMap) {
        List<List<Object>> list = new ArrayList<List<Object>>();
        for (Map<String, Object> map : dataList) {
            List<Object> data = new ArrayList<Object>();
            for (int i = 0; i < dataStrMap.size(); i++) {
                data.add(map.get(dataStrMap.get(i)));
            }
            list.add(data);
        }
        return list;
    }
}
```

4. Excel文档的自动列宽设置

```java
import com.alibaba.excel.enums.CellDataTypeEnum;
import com.alibaba.excel.metadata.CellData;
import com.alibaba.excel.metadata.Head;
import com.alibaba.excel.util.CollectionUtils;
import com.alibaba.excel.write.metadata.holder.WriteSheetHolder;
import com.alibaba.excel.write.style.column.AbstractColumnWidthStyleStrategy;
import org.apache.poi.ss.usermodel.Cell;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ExcelWidthStyleStrategy extends AbstractColumnWidthStyleStrategy {
    private static final int MAX_COLUMN_WIDTH = 255;
    //因为在自动列宽的过程中，有些设置地方让列宽显得紧凑，所以做出了个判断
    private static final int COLUMN_WIDTH = 4;
    private Map<Integer, Map<Integer, Integer>> CACHE = new HashMap(8);

    @Override
    protected void setColumnWidth(WriteSheetHolder writeSheetHolder, List<CellData> cellDataList, Cell cell, Head head, Integer relativeRowIndex, Boolean isHead) {
        boolean needSetWidth = isHead || !CollectionUtils.isEmpty(cellDataList);
        if (needSetWidth) {
            Map<Integer, Integer> maxColumnWidthMap = (Map) CACHE.get(writeSheetHolder.getSheetNo());
            if (maxColumnWidthMap == null) {
                maxColumnWidthMap = new HashMap(16);
                CACHE.put(writeSheetHolder.getSheetNo(), maxColumnWidthMap);
            }

            Integer columnWidth = this.dataLength(cellDataList, cell, isHead);
            if (columnWidth >= 0) {
                if (columnWidth > MAX_COLUMN_WIDTH) {
                    columnWidth = MAX_COLUMN_WIDTH;
                } else {
                    if (columnWidth < COLUMN_WIDTH) {
                        columnWidth = columnWidth * 2;
                    }
                }

                Integer maxColumnWidth = (Integer) ((Map) maxColumnWidthMap).get(cell.getColumnIndex());
                if (maxColumnWidth == null || columnWidth > maxColumnWidth) {
                    ((Map) maxColumnWidthMap).put(cell.getColumnIndex(), columnWidth);
                    writeSheetHolder.getSheet().setColumnWidth(cell.getColumnIndex(), columnWidth * 256);
                }
            }
        }
    }

    private Integer dataLength(List<CellData> cellDataList, Cell cell, Boolean isHead) {
        if (isHead) {
            return cell.getStringCellValue().getBytes().length;
        } else {
            CellData cellData = (CellData) cellDataList.get(0);
            CellDataTypeEnum type = cellData.getType();
            if (type == null) {
                return -1;
            } else {
                switch (type) {
                    case STRING:
                        return cellData.getStringValue().getBytes().length;
                    case BOOLEAN:
                        return cellData.getBooleanValue().toString().getBytes().length;
                    case NUMBER:
                        return cellData.getNumberValue().toString().getBytes().length;
                    default:
                        return -1;
                }
            }
        }
    }
}
```

5. 使用教程

* 获取了List<Map<String, Object>>类型的数据 listDatas
* headMap数组定义的是导出文件表头标题的内容，要按顺序定义
* dataStrMap数据定义的是标题对应的字段名（一定要按顺序对应）

```java
String[]headMap={"项目名称","楼栋名称","单元名称","楼层名称","房间名称","业主/租户姓名","房间状态","房间功能","认证人数"};
        String[]dataStrMap={"hName","bName","uName","fName","pName","cName","pState","pFunction","pNum"};
```

```java
NoModelWriteData d=new NoModelWriteData();
        d.setFileName("认证统计");
        d.setHeadMap(headMap);
        d.setDataStrMap(dataStrMap);
        d.setDataList(listDatas);
        EasyExcelUtils easyExcelUtils=new EasyExcelUtils();
        easyExcelUtils.noModleWrite(d,response);
```

