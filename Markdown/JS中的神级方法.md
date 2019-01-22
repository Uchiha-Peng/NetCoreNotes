## JS中的神级方法

####  URL中的中文乱码

 ```
 var url_string = "http://www.example.com/t.html?a=1&b=3&c=m2-m3-m4-m5"; //window.location.href
 var url = new URL(url_string);
 var c = url.searchParams.get("c");
 console.log(c);
 //先url解码
 var url = new URL(decodeURIComponent(window.location.href));
 var keyword = url.searchParams.get("keyword");
 ```

####自动生成色彩

```javascript
function getRandColor(brightness){
    // Six levels of brightness from 0 to 5, 0 being the darkest
    var rgb = [Math.random() * 256, Math.random() * 256, Math.random() * 256];
    var mix = [brightness*51, brightness*51, brightness*51]; //51 => 255/5
    var mixedrgb = [rgb[0] + mix[0], rgb[1] + mix[1], rgb[2] + mix[2]].map(function(x){ return Math.round(x/2.0)})
    return "rgb(" + mixedrgb.join(",") + ")";
}


function makeRandomColor(){
  var c = '';
  while (c.length < 7) {
    c += (Math.random()).toString(16).substr(-6).substr(-1)
  }
  return '#'+c;
}
```

#### 替换URL不刷新页面

```javascript
  var valiable = window.location.href.split("?")[0];
  window.history.pushState({}, 0, valiable);
```

