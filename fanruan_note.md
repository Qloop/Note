# Note

## 2017.1.16

- Linux解压缩: 

  >  tar -zxvf  ***

- Linux重启命令 

  >  sudo shutdown -r now

- Linux 查看环境变量 

  > vim /etc/profile

- spring-boot 设置支持jsonp

  1. 新建类

     > ```
     > @ControllerAdvice(basePackages = "com.fanruan.controllers")
     > public class JsonpAdvice extends AbstractJsonpResponseBodyAdvice {
     >     public JsonpAdvice(){
     >         super("callback");  //设置支持哪些形式的  比如还可以super("callback","jsonp");
     >     }
     > }
     > ```

     其中 basePackages指定那些包下的controller支持jsonp

  2. 在controller中返回的结果就会被自动包装成  “functioname({json})”的格式。其中functionname就是在请求体中?callback=functioname的。

     >  Warning:  就目前来看，返回只支持domain形式 
     >
     > eg.   return new ReturnInfo(AppConfig.ILLEGAL_ACCESS);

