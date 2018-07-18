#### 遍历嵌套Json生成水平层级Table

```javascript
<script type="text/javascript">
        $(function () {
            var table = $("#table");
            $.ajax({
                url: 'ActionList.aspx',
                type: 'post',
                cache: false,
                data: { type: "getData" },
                datype: 'json',
                success: function (response, textStatus) {
                    table.children().remove();
                    table.append(" <tr><th>一级菜单</th><th>二级菜单</th><th>三级菜单</th><th><input name='chkAll' type='checkbox' value=0>全选</th></tr>");
                    if (response != null && response != "") {
                        var data = JSON.parse(response);
                        $.each(data, function (index1, item) {
                            var html = "";
                            if (item.ChildLevel.length > 0) {
                                //第一层有子菜单
                                html = "<tr><td  rowspan=" + item.RowSpan + ">" + item.RightName + "</td>";
                                var i = 0;
                                $.each(item.ChildLevel, function (index2, items) {
                                    if (items.ChildLevel.length > 0) {
                                        if (i == 0) {
                                            //第二层有子菜单
                                            html += "<td rowspan=" + items.RowSpan + " >" + items.RightName + "</td>";
                                        } else {
                                            //第二层有子菜单
                                            html = "<tr><td rowspan=" + items.RowSpan + " >" + items.RightName + "</td>";
                                        }
                                        var a = 0;
                                        $.each(items.ChildLevel, function (index3, itema) {
                                            if (a == 0) {
                                                //第三层
                                                html += "<td>" + itema.RightName + "</td><td><input name='chk' type='checkbox' value=" + itema.RightId + "></td></tr>";
                                                table.append(html);
                                            }
                                            else {
                                                //第三层
                                                html = "<tr><td>" + itema.RightName + "</td><td><input name='chk' type='checkbox' value=" + itema.RightId + "></td></tr>";
                                                table.append(html);
                                            }
                                            a++;
                                        });

                                    }
                                    else {
                                        if (i == 0) {

                                            //第二层没有子菜单
                                            html += "<td>" + items.RightName + "</td><td></td><td><input name='chk' type='checkbox' value=" + items.RightId + "></td></tr>";
                                            table.append(html);
                                        }
                                        else {
                                            //第二层没有子菜单
                                            html = "<tr><td>" + items.RightName + "</td><td></td><td><input name='chk' type='checkbox' value=" + items.RightId + "></td></tr>";
                                            table.append(html);
                                        }
                                    }
                                    i++;
                                });
                            }
                            else {
                                //第一层没有子菜单
                                html = "<tr><td>" + item.RightName + "</td><td colspan=2></td><td><input name='chk' type='checkbox' value=" + item.RightId + "></td></tr>";
                                table.append(html);
                            }
                            console.info(html);
                        })
                    }
                    console.info(data);
                }, error: function (request) {
                    //console.info(request);
                }
            });
        });
```

