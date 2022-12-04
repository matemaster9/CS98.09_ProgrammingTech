# Excel数据的导入导出

## EasyExcelUtil工具包

{% code title="EasyExcelUtil" %}
```java
@NoArgsConstructor(access = AccessLevel.PRIVATE)
public final class EasyExcelUtil {
    // excel -> List
    public static <T> List<T> toGenericList(InputStream excel, Class<T> tClass) {
        List<T> genericList = new ArrayList<>();
        EasyExcelFactory.read(excel, tClass, new GenericReadListener<>(genericList)).sheet().doRead();
        return genericList;
    }

    // excel导出数据流
    public static void exportExcel(String filename, List<List<String>> head, List<List<Object>> data) throws IOException {
        HttpServletResponse response = getHttpServletResponse(filename);
        if (response == null) {
            return;
        }
        EasyExcelFactory
                .write(response.getOutputStream())
                .head(head)
                .sheet()
                .registerWriteHandler(new LongestMatchColumnWidthStyleStrategy())
                .doWrite(data);
    }

    public static <T> void exportExcel(String filename, List<List<T>> data, Class<T> tClass) throws IOException {
        HttpServletResponse response = getHttpServletResponse(filename);
        if (response == null) {
            return;
        }

        EasyExcelFactory
                .write(response.getOutputStream(), tClass)
                .registerWriteHandler(new LongestMatchColumnWidthStyleStrategy())
                .sheet()
                .doWrite(data);
    }

    private static HttpServletResponse getHttpServletResponse(String filename) throws UnsupportedEncodingException {
        ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes) RequestContextHolder.currentRequestAttributes();
        HttpServletResponse response = servletRequestAttributes.getResponse();
        if (Objects.isNull(response)) {
            return null;
        }

        String encodeFilename = URLEncoder.encode(filename, StandardCharsets.UTF_8.name()).replaceAll("\\+", "%20");
        response.setCharacterEncoding("utf-8");
        response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
        response.setHeader("Content-disposition", "attachment;filename*=utf-8''" + encodeFilename + ".xlsx");

        return response;
    }

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
} 
```
{% endcode %}
