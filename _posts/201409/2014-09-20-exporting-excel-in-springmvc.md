---
layout: post
title: "Exporting excel in SpringMVC"
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
            String sheetName = "姓名-积分清单";
            HSSFSheet sheet = workbook.createSheet(sheetName);

            // create title row
            int rownum = 0;
            HSSFRow header = sheet.createRow(rownum++);
            String[] titles = new String[] { "姓名", "积分" };
            for (int i = 0; i < titles.length; i++) {
                header.createCell(i).setCellValue(titles[i]);
            }

            List<Points> listOfPoints = pointsService.findAllPoints();
            HSSFRow row = null;
            int idx = 0;
            for (Points p : listOfPoints) {
                idx = 0;
                row = sheet.createRow(rownum++);
                row.createCell(idx++).setCellValue(p.getName());
                row.createCell(idx++).setCellValue(p.getPoints());
            }
            String fileName = "listOfPoints.xls";
            response.setHeader("Content-Disposition", "inline; filename=" + fileName);
            response.setContentType("application/vnd.ms-excel");
        }

    };
    return view;
}

{% endhighlight %}

Spring 3.2.6.RELEASE和POI 3.9环境测试通过，中文文件名在各浏览器下的兼容问题不在此文范围。

就酱，嗯。

您有任何问题或建议, 请给我写[邮件](mailto:yinwer81@gmail.com)。
