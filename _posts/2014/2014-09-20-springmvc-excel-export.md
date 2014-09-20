---
layout: post
title: "Export excel with SpringMVC and POI"
categories: programming
---
在SpringMVC中导出excel非常简单，记录如下：
{% highlight java %}
@RequestMapping(value = "/export")
public View export() {

    View view = new AbstractExcelView() {

        @SuppressWarnings("rawtypes")
        protected void buildExcelDocument(Map model, HSSFWorkbook workbook, HttpServletRequest request,
            HttpServletResponse response) throws Exception {

            // create excel sheet
            String sheetName = "requests";
            HSSFSheet sheet = workbook.createSheet(sheetName);

            // create title row
            int rownum = 0;
            HSSFRow header = sheet.createRow(rownum++);
            String[] titles = new String[] { "No.", "Description" };
            for (int i = 0; i < titles.length; i++) {
                header.createCell(i).setCellValue(titles[i]);
            }

            List<Request> list = requestService.findAllRequests();
            HSSFRow row = null;
            int idx = 0;
            for (Request p : list) {
                idx = 0;
                row = sheet.createRow(rownum++);
                row.createCell(idx++).setCellValue(p.getId());
                row.createCell(idx++).setCellValue(p.getDescription());
            }
            String fileName = "requests.xls";
            response.setHeader("Content-Disposition", "inline; filename=" + fileName);
            response.setContentType("application/vnd.ms-excel");
        }

    };
    return view;
}

{% endhighlight %}

导入相关library包，访问http://.../export.xls即可产生excel文件。

Spring 3.2.6.RELEASE, POI 3.9环境测试通过。中文文件名在各浏览器兼容问题不在此文范围。


以上。您有任何问题或建议, 请给我写[邮件](mailto:yinwer81@gmail.com)。

