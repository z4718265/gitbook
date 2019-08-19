# 解决springboot项目请求出现非法字符问题
在springboot2.x.x以上项目或者在tomcat8.5中会出现请求中含有非法字符的错误，前端返回400，后端错误日志如下

```
java.lang.IllegalArgumentException:Invalid character found in the request target. The valid characters are defined in RFC 7230 and RFC 3986
org.apache.coyote.http11.AbstractHttp11Processor process
信息: Error parsing HTTP request header
Note: further occurrences of HTTP header parsing errors will be logged at DEBUG level.
java.lang.IllegalArgumentException: Invalid character found in the request target. The valid characters are defined in RFC 7230 and RFC 3986
at org.apache.coyote.http11.AbstractNioInputBuffer.parseRequestLine(AbstractNioInputBuffer.java:283)
at org.apache.coyote.http11.AbstractHttp11Processor.process(AbstractHttp11Processor.java:1017)
at org.apache.coyote.AbstractProtocol$AbstractConnectionHandler.process(AbstractProtocol.java:684)
at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1524)
at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.run(NioEndpoint.java:1480)
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
at java.lang.Thread.run(Thread.java:745)
```
这段报错的意思是：请求中含有无效字符，有效的字符在RFC 7230和RFC 3986中定义

<font color=red>
出现这个错误的原因是：我们在前后台交互的时候往往使用json格式的字段串参数，其中含有“{}”“[]”这些特舒符号，在高版本的tomcat中含有这些字符的请求会被拦截
</font>

查看springboot项目的tomcat版本：
###### 方法一：使用idea作为开发工具的可以直接点击项目下面External Libraries，搜索字符串“tomcat”可以看到tomcat版本为9.0.19
###### 方法二：打开本地maven仓库springboot父依赖配置，路径如：C:\Users\Administrator\.m2\repository\org\springframework\boot\spring-boot-dependencies
我是2.1.5版本的springboot，打开2.1.5.RELEASE文件夹查看 spring-boot-dependencies-2.1.5.RELEASE.pom 里面的配置 搜索tomcat.version，显示<tomcat.version>9.0.19</tomcat.version>
这就是springboot所依赖的tomcat的版本

<font color=red>
解决办法：
1.降低tomcat版本（不推荐），将tomcat版本改到tomcat8.5以下，但是我并不推荐这种办法，因为你以后的开发迟早要使用更新的版本，所以怎么修改tomcat版本我就不介绍了
2.在springboot工程中增加一个tomcat 配置，或者将webServerFactory方法加入到springboot启动类中，配置文件代码如下：
</font>

```java
@Configuration
public class TomcatConfig {

    @Bean
    public TomcatServletWebServerFactory webServerFactory() {
        TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
        factory.addConnectorCustomizers((Connector connector) -> {
            connector.setProperty("relaxedPathChars", "\"<>[\\]^`{|}");
            connector.setProperty("relaxedQueryChars", "\"<>[\\]^`{|}");
        });
        return factory;
    }
}
```
