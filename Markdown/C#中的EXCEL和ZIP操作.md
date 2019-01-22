##C#中的EXCEL和ZIP操作

##### 使用Epplus导出多页Excel

```c#
public static byte[] ExportExcel(List<AnnualReportIndexViewModels> dataList, string Year)
        {
            var path = Path.Combine(HttpContext.Current.Server.MapPath("~"), "data.xlsx");
            try
            {

                if (File.Exists(path))
                    File.Delete(path);
                FileInfo newFile = new FileInfo(path);
                using (ExcelPackage package = new ExcelPackage(newFile))
                {
                    int rowCount = 51;
                    int columnCout = 5;
                    if (dataList == null || dataList.Count == 0)
                        throw new Exception("列表数据不能为空!");
                    foreach (var item in dataList)
                    {
                        int currentRow = 1;
                        if (item.Workload == null || item.Workload.Count != 10)
                            continue;

                        if (item.TreatmentQuality == null || item.TreatmentQuality.Count != 24)
                            continue;

                        if (item.MedicalEfficiency == null || item.MedicalEfficiency.Count != 5)
                            continue;

                        if (item.CostSituation == null || item.CostSituation.Count != 6)
                            continue;
                        ExcelWorksheet worksheet = package.Workbook.Worksheets.Add(Year + "年医疗质控年报指标" + item.DepName);
                        worksheet.Cells.Style.HorizontalAlignment = ExcelHorizontalAlignment.Left;
                        worksheet.Cells.Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                        worksheet.Row(1).Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                        worksheet.Row(2).Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                        worksheet.Row(1).Style.Font.Size = 16;
                        int i = 1;
                        //设置行高
                        while (i <= rowCount)
                        {
                            worksheet.Row(i).Height = i == 1 ? 22.5 : 16.5;
                            int col = 1;
                            while (col <= 5)
                            {
                                worksheet.Cells[i, col].Style.Border.Top.Style = ExcelBorderStyle.Thin;
                                worksheet.Cells[i, col].Style.Border.Bottom.Style = ExcelBorderStyle.Thin;
                                worksheet.Cells[i, col].Style.Border.Right.Style = ExcelBorderStyle.Thin;
                                worksheet.Cells[i, col].Style.Border.Left.Style = ExcelBorderStyle.Thin;
                                col++;
                            }
                            i++;
                        }
                        i = 1;
                        //设置列宽
                        while (i <= columnCout)
                        {
                            worksheet.Column(i).Width = i == 2 ? 25 : 10;
                            i++;
                        }
                        //第一行
                        worksheet.Cells[currentRow, 1].Value = Year + "年医疗质控年报指标";
                        worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                        currentRow++;
                        //第二行
                        worksheet.Cells[currentRow, 1].Value = "序号";
                        worksheet.Cells[currentRow, 2].Value = "指标名称";
                        worksheet.Cells[currentRow, 3].Value = "数值";
                        worksheet.Cells[currentRow, 4].Value = "去年数值";
                        worksheet.Cells[currentRow, 5].Value = "同比";
                        currentRow++;
                        //第三行
                        worksheet.Cells[currentRow, 1].Value = "一、工作负荷";
                        worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                        currentRow++;
                        foreach (var vm in item.Workload)
                        {
                            worksheet.Cells[currentRow, 1].Value = vm.Sort;
                            worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                            worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                            worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                            worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                            worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                            currentRow++;
                        }
                        //第N行
                        worksheet.Cells[currentRow, 1].Value = "二、治疗质量";
                        worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                        currentRow++;

                        foreach (var vm in item.TreatmentQuality)
                        {
                            worksheet.Cells[currentRow, 1].Value = vm.Sort;
                            worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                            worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                            worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                            worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                            worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                            currentRow++;
                        }
                        //第N行
                        worksheet.Cells[currentRow, 1].Value = "三、医疗效率";
                        worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                        currentRow++;

                        foreach (var vm in item.MedicalEfficiency)
                        {
                            worksheet.Cells[currentRow, 1].Value = vm.Sort;
                            worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                            worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                            worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                            worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                            worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                            currentRow++;
                        }
                        //第N行
                        worksheet.Cells[currentRow, 1].Value = "四、费用情况";
                        worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                        currentRow++;

                        foreach (var vm in item.CostSituation)
                        {
                            worksheet.Cells[currentRow, 1].Value = vm.Sort;
                            worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                            worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                            worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                            worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                            worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                            currentRow++;
                        }
                    }
                    return package.GetAsByteArray();
                }
            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
        }
```

#### 使用Epplus导出多个Excel，并且生成zip文件

```c#
public static byte[] ExportZip(List<AnnualReportIndexViewModels> dataList, string Year)
        {
            if (dataList == null || dataList.Count == 0)
                throw new Exception("列表数据不能为空!");
            try
            {
                using (var memoryStream = new MemoryStream())
                {
                    using (var archive = new ZipArchive(memoryStream, ZipArchiveMode.Create, true))
                    {
                        int rowCount = 51;
                        int columnCout = 5;
                        foreach (var item in dataList)
                        {
                            if (item.Workload == null || item.Workload.Count != 10)
                                continue;

                            if (item.TreatmentQuality == null || item.TreatmentQuality.Count != 24)
                                continue;

                            if (item.MedicalEfficiency == null || item.MedicalEfficiency.Count != 5)
                                continue;

                            if (item.CostSituation == null || item.CostSituation.Count != 6)
                                continue;
                            var path = Path.Combine(HttpContext.Current.Server.MapPath("~"), Year + "年医疗质控年报指标_" + item.DepName + ".xlsx");
                            if (File.Exists(path))
                                File.Delete(path);
                            FileInfo newFile = new FileInfo(path);
                            using (ExcelPackage package = new ExcelPackage(newFile))
                            {
                                int currentRow = 1;
                                ExcelWorksheet worksheet = package.Workbook.Worksheets.Add(Year + "年医疗质控年报指标_" + item.DepName);
                                worksheet.Cells.Style.HorizontalAlignment = ExcelHorizontalAlignment.Left;
                                worksheet.Cells.Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                                worksheet.Row(1).Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                worksheet.Row(2).Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                worksheet.Row(1).Style.Font.Size = 16;
                                int i = 1;
                                //设置行高
                                while (i <= rowCount)
                                {
                                    worksheet.Row(i).Height = i == 1 ? 22.5 : 16.5;
                                    int col = 1;
                                    while (col <= 5)
                                    {
                                        worksheet.Cells[i, col].Style.Border.Top.Style = ExcelBorderStyle.Thin;
                                        worksheet.Cells[i, col].Style.Border.Bottom.Style = ExcelBorderStyle.Thin;
                                        worksheet.Cells[i, col].Style.Border.Right.Style = ExcelBorderStyle.Thin;
                                        worksheet.Cells[i, col].Style.Border.Left.Style = ExcelBorderStyle.Thin;
                                        col++;
                                    }
                                    i++;
                                }
                                i = 1;
                                //设置列宽
                                while (i <= columnCout)
                                {
                                    worksheet.Column(i).Width = i == 2 ? 25 : 10;
                                    i++;
                                }
                                //第一行
                                worksheet.Cells[currentRow, 1].Value = Year + "年医疗质控年报指标";
                                worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                                currentRow++;
                                //第二行
                                worksheet.Cells[currentRow, 1].Value = "序号";
                                worksheet.Cells[currentRow, 2].Value = "指标名称";
                                worksheet.Cells[currentRow, 3].Value = "数值";
                                worksheet.Cells[currentRow, 4].Value = "去年数值";
                                worksheet.Cells[currentRow, 5].Value = "同比";
                                currentRow++;
                                //第三行
                                worksheet.Cells[currentRow, 1].Value = "一、工作负荷";
                                worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                                currentRow++;
                                foreach (var vm in item.Workload)
                                {
                                    worksheet.Cells[currentRow, 1].Value = vm.Sort;
                                    worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                    worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                                    worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                                    worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                                    worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                                    currentRow++;
                                }
                                //第N行
                                worksheet.Cells[currentRow, 1].Value = "二、治疗质量";
                                worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                                currentRow++;

                                foreach (var vm in item.TreatmentQuality)
                                {
                                    worksheet.Cells[currentRow, 1].Value = vm.Sort;
                                    worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                    worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                                    worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                                    worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                                    worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                                    currentRow++;
                                }
                                //第N行
                                worksheet.Cells[currentRow, 1].Value = "三、医疗效率";
                                worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                                currentRow++;

                                foreach (var vm in item.MedicalEfficiency)
                                {
                                    worksheet.Cells[currentRow, 1].Value = vm.Sort;
                                    worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                    worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                                    worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                                    worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                                    worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                                    currentRow++;
                                }
                                //第N行
                                worksheet.Cells[currentRow, 1].Value = "四、费用情况";
                                worksheet.Cells[currentRow, 1, currentRow, columnCout].Merge = true;
                                currentRow++;

                                foreach (var vm in item.CostSituation)
                                {
                                    worksheet.Cells[currentRow, 1].Value = vm.Sort;
                                    worksheet.Cells[currentRow, 1].Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
                                    worksheet.Cells[currentRow, 2].Value = vm.ReportName;
                                    worksheet.Cells[currentRow, 3].Value = vm.CurrentValue;
                                    worksheet.Cells[currentRow, 4].Value = vm.PastValue;
                                    worksheet.Cells[currentRow, 5].Value = vm.ContrastValue;
                                    currentRow++;
                                }
                                ZipArchiveEntry zip = archive.CreateEntry(Year + "年医疗质控年报指标_" + item.DepName + ".xlsx");
                                using (var entryStream = zip.Open())
                                {
                                    byte[] array = package.GetAsByteArray();
                                    entryStream.Write(array, 0, array.Length);
                                }
                            }
                        }

                    }
                    return memoryStream.ToArray();
                }
            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
        }
```

#### 精简版Demo版导出多个Excel打包

```
static void Main(string[] args)
        {
            using (var memoryStream = new MemoryStream())
            {
                using (var archive = new ZipArchive(memoryStream, ZipArchiveMode.Create, true))
                {
                    int i = 1;
                    while (i < 3)
                    {
                        FileInfo fl = new FileInfo(i + ".xlsx");
                        //Create a new ExcelPackage
                        using (ExcelPackage excelPackage = new ExcelPackage(fl))
                        {
                            //Create the WorkSheet
                            ExcelWorksheet worksheet = excelPackage.Workbook.Worksheets.Add("Sheet 1");
                            //Add some text to cell A1
                            worksheet.Cells["A1"].Value = i * 1000;
                            //You could also use [line, column] notation:
                            worksheet.Cells[1, 2].Value = "This is cell B1!";
                            ZipArchiveEntry zip = archive.CreateEntry(i + ".xlsx");
                            using (var entryStream = zip.Open())
                            {
                                byte[] array = excelPackage.GetAsByteArray();
                                entryStream.Write(array, 0, array.Length);
                            }
                        }

                        i++;
                    }

                }
                //读出字节数组提供客户端下载
                byte[] bytes= memoryStream.ToArray();

                //保存到本地
                using (var fileStream = new FileStream(@"D:\test.zip", FileMode.Create))
                {
                    memoryStream.Seek(0, SeekOrigin.Begin);
                    memoryStream.CopyTo(fileStream);
                }
            }
        }
```

