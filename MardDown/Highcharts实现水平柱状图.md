#### 使用Highcharts实现水平的柱状图

##### 效果如下

![](images\barchart.png)

##### 代码如下

```html
<!DOCTYPE HTML>
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>Highcharts Example</title>
		<script src="https://cdn.hcharts.cn/highcharts/highcharts.js"></script>
	</head>
	<body>
<div id="container" style="min-width: 310px; height: 400px; margin: 0 auto"></div>
		<script type="text/javascript">
			Highcharts.chart('container', {
                chart: {
                    type: 'bar',
                    inverted: true
                },
                title: {
                    text: '页面使用排行'
                },
                subtitle: {
                    text: '小标题'
                },
                legend: {
                    enabled: false
                },
                xAxis: {
                    categories: ['Jan', 'Feb', 'Mar', 'Apr', 'May']
                },
                yAxis: {
                    visible: false
                },
                tooltip: {
                    valueSuffix: '°C'
                },
                plotOptions: {
                    columnrange: {
                        dataLabels: {
                            enabled: true,
                            format: '{y}°C'
                        }
                    }
                },
                legend: {
                    enabled: false
                },
                series: [{
                    name: 'Temperatures',
                    data: [49.9, 71.5, 106.4, 129.2, 144.0]
                }]

            });
		</script>
	</body>
</html>

```

