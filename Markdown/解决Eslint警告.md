## 使用 npm run lint解决Eslint控制台警告

1. 既然控制台有Eslint的警告，说明Eslint肯定是装了的，打开package.json，搜索“lint”，如下图：

![](images/eslint.png)

2. 在eslint后面插入--fix，注意前后都有一个空格
3. 然后终端运行npm run lint就可以解决错误了，再运行项目，警告已经消失了