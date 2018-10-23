## ASP.NET MVC/Web API上传文件

#### 通过mult/form-data向Web API上传文件

##### 前端代码

> ```html
> <form class="form-horizontal" method="post" enctype="multipart/form-data" action="/api/UpLoadFile/UpLoadLocalMap">
>             <div class="form-group">
>                 <label class="control-label col-md-2" for="H_IpPort">IpPort</label>
>                 <div class="col-md-3">
>                     <input class="form-control text-box single-line" id="IpPort" placeholder="请输入内网IP和端口http://IP:Port" name="IpPort" type="text" required>
>                 </div>
>             </div>
> 
>             <div class="form-group">
>                 <label class="control-label col-md-2" for="JsonFile">JsonFile</label>
>                 <div class="col-md-3">
>                     <input accept="application/json" class="form-control" id="JsonFile" name="JsonFile" required="required" type="file" />
>                 </div>
>             </div>
> 
>             <div class="form-group">
>                 <div class="col-md-offset-2 col-md-10">
>                     <input type="submit" id="btn" value="上传" class="btn btn-default">
>                 </div>
>             </div>
>         </form>
> ```

##### Web API(接收multipart/form-data)

> ```c#
> public async Task<IHttpActionResult> UpLoadLocalMap()
>         {
>             //检查请求是否包含multipart/form-data
>             if (!ModelState.IsValid || !Request.Content.IsMimeMultipartContent())
>             {
>                 return BadRequest("Content-Type有误!");
>             }
>             try
>             {
>                 var provider = new MultipartMemoryStreamProvider();
>                 await Request.Content.ReadAsMultipartAsync(provider);
> 
>                 string IpPort = string.Empty, style = HttpContext.Current.Server.MapPath("~/Mapbox/style"), mappath = Path.Combine(HttpContext.Current.Server.MapPath("~/Mapbox"), "3d.json");
>                 //判断文件夹是否存在,不存在就创建
>                 if (!Directory.Exists(style))
>                 {
>                     Directory.CreateDirectory(style);
>                 }
>                 style = Path.Combine(style, "style.json");
>                 foreach (var item in provider.Contents)
>                 {
>                     if (item.Headers.ContentDisposition.Name.Contains("IpPort"))
>                     {
>                         IpPort = await item.ReadAsStringAsync();
>                     }
>                     else if (item.Headers.ContentDisposition.Name.Contains("JsonFile"))
>                     {
>                         byte[] bytearray = await item.ReadAsByteArrayAsync();
>                         using (FileStream fs = new FileStream(mappath, FileMode.Create))
>                         {
>                             //将byte数组写入文件中
>                             fs.Write(bytearray, 0, bytearray.Length);
>                         }
>                         if (File.Exists(mappath))
>                         {
>                             if (File.Exists(style))
>                             {
>                                 string json = File.ReadAllText(style);
>                                 dynamic styleObj = JsonConvert.DeserializeObject<dynamic>(json);
>                                 if (styleObj != null && styleObj.sprite != null && styleObj.glyphs != null)
>                                 {
>                                     //IpPort = IpPort.Contains("http") ? IpPort : "http://" + IpPort;
>                                     styleObj.sprite = IpPort + "/Mapbox/sprite/sprite";
>                                     styleObj.glyphs = IpPort + "/Mapbox/{fontstack}/{range}.pbf";
>                                     File.WriteAllText(style, JsonConvert.SerializeObject(styleObj));
>                                     return Json(new { Message = "上传成功" });
>                                 }
>                                 else
>                                 {
>                                     return BadRequest("style文件格式有误");
>                                 }
>                             }
>                             return BadRequest("style文件不存在");
>                         }
>                         return BadRequest("3d.json文件上传失败");
>                     }
>                     else
>                     {
>                         return BadRequest("参数获取错误!");
>                     }
>                 }
>             }
>             catch (Exception ex)
>             {
>                 return BadRequest(ex.Message);
>             }
>             return BadRequest("上传失败!");
>         }
> ```
>
>

#### MVC中实现文件上传

##### ViewModel

> ```c#
>   public class JsonFileViewModel
>     {
>         public string H_IpPort { get; set; }
>         public HttpPostedFileBase JsonFile { get; set; }
>     }
> ```

##### Razor Page

> ```c#
> @using (Html.BeginForm("UpLoad", "Hospital", FormMethod.Post, new { enctype = "multipart/form-data" }))
> {
> 
>     @Html.AntiForgeryToken()
>     <div class="form-horizontal">
>         <hr />
>         @Html.ValidationSummary(true, "", new { @class = "text-danger" })
>         <div class="form-group">
>             @Html.LabelFor(model => model.H_IpPort, htmlAttributes: new { @class = "control-label col-md-2" })
>             <div class="col-md-10">
>                 @Html.EditorFor(model => model.H_IpPort, new { htmlAttributes = new { @class = "form-control",@readonly="readonly" } })
>                 @Html.ValidationMessageFor(model => model.H_IpPort, "", new { @class = "text-danger" })
>             </div>
>         </div>
> 
>         <div class="form-group">
>             @Html.LabelFor(model => model.JsonFile, htmlAttributes: new { @class = "control-label col-md-2" })
>             <div class="col-md-10">
>                 @Html.TextBoxFor(model => model.JsonFile, new { htmlAttributes = new { @class = "form-control" }, type = "file", accept = "application/json", required = "required" })
>                 @Html.ValidationMessageFor(model => model.JsonFile, "", new { @class = "text-danger" })
>             </div>
>         </div>
> 
>         <div class="form-group">
>             <div class="col-md-offset-2 col-md-10">
>                 <input type="submit" value="上传" class="btn btn-default" />
>             </div>
>         </div>
>     </div>
> }
> ```

##### Action (接收ViewModel)

> ```c#
> public async Task<ActionResult> UpLoad(JsonFileViewModel fileVM)
>         {
> 
>             string message = string.Empty;
>             if (!ModelState.IsValid | fileVM.JsonFile == null || fileVM.JsonFile.ContentLength == 0 || fileVM.JsonFile.ContentType != "application/json")
>             {
>                 message = "请上传有效的json文件";
>             }
>             else
>             {
>                 string json = string.Empty;
>                 try
>                 {
>                     byte[] buffer;
>                     using (BinaryReader b = new BinaryReader(fileVM.JsonFile.InputStream))
>                     {
>                         buffer = b.ReadBytes(fileVM.JsonFile.ContentLength);
>                     }
>                     using (HttpClient client = new HttpClient())
>                     {
>                         ByteArrayContent content = new ByteArrayContent(buffer);
>                         HttpResponseMessage resp = await client.PostAsync($@"{fileVM.H_IpPort}/api/UpLoadFile/UpLoadMapFile?FileName=3d.json&IpPort={fileVM.H_IpPort}", content);
>                         message = JsonConvert.DeserializeObject<dynamic>(await resp.Content.ReadAsStringAsync()).Message;
>                     }
>                 }
>                 catch (Exception ex)
>                 {
>                     message = ex.Message;
>                 }
>             }
>             CommonTools.WriteLog("Error", message);
>             ViewBag.Message = message;
>             return View();
>         }
> ```
>
>

##### API(接收ByteArrayContent接收Binary字节数组)

> ```c#
> public async Task<IHttpActionResult> UpLoadMapFile([FromUri] string FileName, [FromUri]string IpPort)
>         {
>             if (!ModelState.IsValid)
>             {
>                 return BadRequest(ModelState);
>             }
>             try
>             {
>                 string style = HttpContext.Current.Server.MapPath("~/Mapbox/style");
>                 //判断文件夹是否存在,不存在就创建
>                 if (!Directory.Exists(style))
>                 {
>                     Directory.CreateDirectory(style);
>                 }
>                 style = Path.Combine(style, "style.json");
>                 string mappath = Path.Combine(HttpContext.Current.Server.MapPath("~/Mapbox"), FileName);
>                 byte[] bytearray = await this.Request.Content.ReadAsByteArrayAsync();
>                 if (bytearray.Length > 0)
>                 {
>                     using (FileStream fs = new FileStream(mappath, FileMode.Create))
>                     {
>                         //将byte数组写入文件中
>                         fs.Write(bytearray, 0, bytearray.Length);
>                     }
>                     if (File.Exists(mappath))
>                     {
>                         if (File.Exists(style))
>                         {
>                             string json = File.ReadAllText(style);
>                             dynamic styleObj = JsonConvert.DeserializeObject<dynamic>(json);
>                             if (styleObj != null && styleObj.sprite != null && styleObj.glyphs != null)
>                             {
>                                 //IpPort = IpPort.Contains("http") ? IpPort : "http://" + IpPort;
>                                 styleObj.sprite = IpPort + "/Mapbox/sprite/sprite";
>                                 styleObj.glyphs = IpPort + "/Mapbox/{fontstack}/{range}.pbf";
>                                 File.WriteAllText(style, JsonConvert.SerializeObject(styleObj));
>                                 return Json(new { Message = "上传成功" });
>                             }
>                             else
>                             {
>                                 return BadRequest("style文件格式有误");
>                             }
>                         }
>                         return BadRequest("style文件不存在");
>                     }
>                     return BadRequest("3d.json文件上传失败");
>                 }
>                 return BadRequest("文件大小不能为0字节");
>             }
>             catch (Exception ex)
>             {
>                 return BadRequest($"Error：{ex.Message}");
>             }
> 
>         }
> ```
>
>