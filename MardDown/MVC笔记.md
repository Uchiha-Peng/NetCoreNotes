1. 跳过默认页，跳转到指定视图
   Return View("XXX");

2. ViewBag和ViewData
   ViewBag.Message=""; ViewData["Message]="";传递少量数据，没什么不同之处，ViewData是一种特殊的VIewDataDictionary，ViewData["This Is"]="";ViewBag就无法实现

3. View中显示@符号
   使用@@转义

4. Razor引擎默认使用HTML编码，即便输入Js脚本字符串也不会执行，如果想使用字符串执行JS
   @Html.Raw();

5. 直接输出文本
   @:Out Put Text To Page !

6. 对用户输入进行编码，避免XSS攻击
   @Ajax.JavaScriptStringEncode(xxx);

