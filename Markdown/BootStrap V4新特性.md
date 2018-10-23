## BootStrap V4新特性

1. 屏幕大小由四种增加到五种：.col-	.col-sm-	.col-md-	.col-lg-	.col-xl-
2. 支持自动布局列：class="col"会自动生成等分的列

```
<div class="container">
  <div class="row">
    <div class="col">
      1 of 2
    </div>
    <div class="col">
      2 of 2
    </div>
  </div>
 </div>
```

3. 支持自动改变宽度的列：class=“col-md-auto”