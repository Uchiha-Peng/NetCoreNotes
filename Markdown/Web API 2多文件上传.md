## Web API 2多文件上传 multpart/form-data

##### Html中的客户端代码

```
<form name="form1" method="post" enctype="multipart/form-data" action="api/UpLoadFile/UpLoadFile">
        <div>
            <label for="caption">Image Caption</label>
            <input name="F_ID" type="text" />
        </div>
        <div>
            <label for="image1">Image File</label>
            <input name="image1" type="file" />
        </div>
        <div>
            <label for="image2">Image File</label>
            <input name="image2" type="file" />
        </div>
        <div>
            <input type="submit" value="Submit" />
        </div>
    </form>
```

##### Web API中的服务端代码

```
 public async Task<IHttpActionResult> UpLoadFile()
        {
            //检查请求是否包含multipart/form-data
            if (!Request.Content.IsMimeMultipartContent())
            {
                return BadRequest();
            }
            string F_ID = string.Empty;
            string root = HttpContext.Current.Server.MapPath("~/Image");
            if (!Directory.Exists(root))
            {
                Directory.CreateDirectory(root);
            }
            MultipartFormDataStreamProvider provider = new MultipartFormDataStreamProvider(root);
            try
            {
                //保持响应正文
                //读取表单数据并返回异步任务。
                await Request.Content.ReadAsMultipartAsync(provider);
                //这说明了如何获取表单数据。
                if (provider.FormData.AllKeys.Length > 0)
                {
                    F_ID = provider.FormData.GetValues("F_ID")[0];
                }
                if (provider.FileData.Count > 0)
                {
                    //这说明了如何获取上传文件的文件名。
                    foreach (MultipartFileData file in provider.FileData)
                    {
                        string FileName = Path.Combine(root, file.Headers.ContentDisposition.FileName.Replace('\"', ' ').Trim());
                        FileInfo fileInfo = new FileInfo(file.LocalFileName);
                        fileInfo.MoveTo(FileName);
                    }
                }
                return Ok();
            }
            catch (Exception ex)
            {
                return BadRequest("文件上传出错！");
            }
        }
```

