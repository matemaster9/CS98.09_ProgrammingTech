---
description: 基于EasyExcel读取Excel数据
---

# Excel导入

easyexcel



```java
@AllArgsConstructor
private static class GenericReadListener<T> implements ReadListener<T> {
    private final List<T> dataList;

    @Override
    public void invoke(T t, AnalysisContext analysisContext) {
        dataList.add(t);
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {
        // ignore
    }
}
```

```java
public static <T> List<T> toGenericList(InputStream excel, Class<T> tClass) {
    List<T> genericList = new ArrayList<>();
    EasyExcelFactory.read(excel, tClass, new GenericReadListener<>(genericList)).sheet().doRead();
    return genericList;
}
```
