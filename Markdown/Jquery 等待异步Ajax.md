## Jquery 等待异步Ajax
#### Jquery 等待异步Ajax

```javascript
  //Ajax请求
  function GetJsonData() {
            console.info("开始执行");
            return $.ajax({
                url: "http://47.98.243.75:8015/Mapbox/3d.json",
                type: "GET",
                dataType: "json",
                // success: function (data) {
                //     //楼层倒序排序
                //     if (data == null || data == "") {
                //         console.info("请求到的数据为空！");
                //     }
                //     jsonData = data;
                // }
            });
        }
        
$(function () {
    //GetJsonData()执行完毕再执行done()方法中的逻辑，data为Ajax的返回值
            $.when(GetJsonData()).done(function (data) {
                console.info("执行完毕");
                if (data != null && data.floors != undefined && data.center != undefined) {
                    jsonData = data;
                    LoadMap();
                    ChangeFloor();
                }
                else {
                    console.info("执行结束,返回值为空");
                }
            });
        });
```

#### 和Array数组排序

```javascript
//按照json对象数组中的某个字段排序
var floors = jsonData.floors.sort((a, b) => {
    //从大到小倒序
                    return Number(b.name) - Number(a.name);
                });
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="css/mapbox-gl-ips.css">
    <link rel="stylesheet" href="css/mapbox-formsass.css">
    <style>
        html,
        body {
            width: 100%;
            height: 100%;
            padding: 0px;
            margin: 0px;
        } 
          #floorList {
            height: 130px;
            width: 40px;
            padding: 20px 0px;
            border: 1px solid #F1F1F1;
            border-radius: 3px;
            box-shadow: 2px;
            position: absolute;
            left:  15px;
            bottom: 50px;
            background-color:white;
            display: flex;
            color: #5c5c5c;
            flex-flow: column nowrap;
            justify-content:center;
            align-items: center;
            overflow-y: scroll

        }

        /* for Chrome */
        #floorList::-webkit-scrollbar {
            display: none;
        }

        #floorList .floorItem {
            display: block;
            width: 40px;
            height: 30px;            
            font-size: 20px;
            text-align: center;
        }

       #floorList .active{
            color:#06b99a;
            background-color: aliceblue
        }
        </style>
</head>

<body>

    <div id='map' style='width: 100%; height: 100%;'></div>
    <ul id="floorList">
    </ul>
    <script type="text/javascript" src="js/jquery v3.3.1.js"></script>
    <script type="text/javascript" src="js/mapbox-gl-ips.js"></script>
    <script type="text/javascript">
        var currentMap;
        var jsonData;
        var floorArray = [];

        function GetJsonData() {
            console.info("开始执行");
            return $.ajax({
                url: "http://47.98.243.75:8015/Mapbox/3d.json",
                type: "GET",
                dataType: "json",
                // success: function (data) {
                //     //楼层倒序排序
                //     if (data == null || data == "") {
                //         console.info("请求到的数据为空！");
                //     }
                //     jsonData = data;
                // }
            });
        }

        function LoadMap() {

            mapboxgl.accessToken = 'pk.eyJ1IjoieHpxMjMyNSIsImEiOiJjajN6a2NvMjgwNGV0MzNwY3A4OXgwY2QzIn0.mQXfGdPMW-eQ4zY_0vdmzg';
            var map = new mapboxgl.Map({
                container: 'map',
                style: 'http://47.98.243.75:8015/Mapbox/style/style.json',//style json
                localIdeographFontFamily: '"Noto Sans", "Noto Sans CJK SC"',//字体
                center: jsonData.center,//中心点
                zoom: 17.52,//默认缩放比例
                bearing: 9.4,
                pitch: 0//角度
            });
            map.addControl(new mapboxgl.NavigationControl());
            map.on("load", function () {

                $("#floorList").children().remove();
                var floors = jsonData.floors.sort((a, b) => {
                    return Number(b.name) - Number(a.name);
                });
                console.info(floors);
                $.each(floors, function (i, v) {
                    if (v.name == "-20") {
                        map.getSource('-20').setData(v.data.geojson);
                    }
                    else {
                        if (v.name == "1") {
                            map.getSource('composite').setData(v.data.geojson);
                            $("#floorList").append('<li class="floorItem active" data-index=' + i + ' >' + v.desc + '</li>');
                        }
                        else {
                            $("#floorList").append('<li class="floorItem" data-index=' + i + ' >' + v.desc + '</li>');
                        }
                    }
                    floorArray.push({ "name": v.desc, "data": v.data.geojson });
                });
            });
            currentMap = map;
        }

        function ChangeFloor() {
            $("#floorList").on('click', '.floorItem', function () {
                var index = $(this).data("index");
                $(".floorItem").css({ "color": "#5c5c5c", "background-color": "white" });
                $(this).css({ "color": "#06b99a", "background-color": "aliceblue" });
                currentMap.getSource('composite').setData(floorArray[index].data);
            });
        }

        $(function () {
            $.when(GetJsonData()).done(function (data) {
                console.info("执行完毕");
                if (data != null && data.floors != undefined && data.center != undefined) {
                    jsonData = data;
                    LoadMap();
                    ChangeFloor();
                }
                else {
                    console.info("执行结束,返回值为空");
                }
            });
        });
    </script>
</body>

</html>
```

