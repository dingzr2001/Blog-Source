# 使用vue和elementui遇到的各种坑

### 正则表达式报错

一般是因为其中的反斜杠解析错误，在后面加上

```
// eslint-disable-line
```

这个注释就好

###  el-form几种表单验证方式

https://blog.csdn.net/weixin_45046532/article/details/137624361

### 常用表单域验证正则匹配

https://blog.csdn.net/2401_84092423/article/details/137700320

### 嵌套el-form-item

https://blog.csdn.net/weixin_69670563/article/details/135880729

### el-form里出现一个item包含多个输入，怎样进行表单域验证？



### 邮箱验证

```
/^\\s*\\w+(?:\\.{0,1}[\\w-]+)*@[a-zA-Z0-9]+(?:[-.][a-zA-Z0-9]+)*\\.[a-zA-Z]+\\s*$/
```

/^\\s*\\w+(?:\\.{0,1}[\\w-]+)*@[a-zA-Z0-9]+(?:[-.][a-zA-Z0-9]+)*\\.[a-zA-Z]+\\s*$/

87558353