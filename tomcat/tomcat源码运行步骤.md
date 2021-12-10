## 源码编译运行
1. mkdir study
2. cd study
3. git clone https://github.com/apache/tomcat.git
4. maven配置
    - cd tomcat
    - 创建pom.xml，内容如下
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

            <modelVersion>4.0.0</modelVersion>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>apache_tomcat_master</artifactId>
            <name>Tomcat10.1</name>
            <version>10.1</version>

            <build>
                <finalName>Tomcat10.1</finalName>
                <sourceDirectory>java</sourceDirectory>
            <!-- <testSourceDirectory>test</testSourceDirectory>-->
                <resources>
                    <resource>
                        <directory>java</directory>
                    </resource>
                </resources>

            <!-- <testResources>
                    <testResource>
                        <directory>test</directory>
                    </testResource>
                </testResources>-->
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.5.1</version>
                        <configuration>
                            <encoding>UTF鈥?</encoding>
                            <source>10</source>
                            <target>10</target>
                        </configuration>
                    </plugin>
                </plugins>
            </build>
            <dependencies>
                <dependency>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                    <version>4.12</version>
                    <scope>test</scope>
                </dependency>
                <dependency>
                    <groupId>org.easymock</groupId>
                    <artifactId>easymock</artifactId>
                    <version>3.4</version>
                </dependency>
                <dependency>
                    <groupId>ant</groupId>
                    <artifactId>ant</artifactId>
                    <version>1.7.0</version>
                </dependency>
                <dependency>
                    <groupId>wsdl4j</groupId>
                    <artifactId>wsdl4j</artifactId>
                    <version>1.6.2</version>
                </dependency>
                <dependency>
                    <groupId>javax.xml</groupId>
                    <artifactId>jaxrpc</artifactId>
                    <version>1.1</version>
                </dependency>
                <dependency>
                    <groupId>org.eclipse.jdt.core.compiler</groupId>
                    <artifactId>ecj</artifactId>
                    <version>4.5.1</version>
                </dependency>
            </dependencies>
        </project>
        ```
        ![](.\res\pom_location.png)
5. idea导入项目
    - 选择上面创建的pom.xml导入项目
    ![](.\res\open_project.png)
6. 运行
    - 运行配置
        1. 点击面板右上方Add configurations
        ![](.\res\add_config.png)
        2. 点击左上角+ -> Application
        ![](.\res\add_config_1.png)
        3. 右侧面版填name、main class、jre -> 点击右下角OK按钮
            - main class: org.apache.catalina.startup.Bootstrap
            - vm options:
                ```
                -Dcatalina.home=E:\learning\github\public\study\catalina-home
                -Dcatalina.base=E:\learning\github\public\study\catalina-home
                -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
                -Djava.util.logging.config.file=E:\learning\github\public\study\catalina-home\conf\logging.properties
                ```
            - jre: Default(11.0.6) # idea自带jre
        ![](.\res\add_config_2.png)

    - 点击右上角运行按钮(右三角形)
        ![](.\res\run.png)
        1. Error:(42, 35) java: 程序包org.apache.tomcat.jakartaee不存在
            - 解决方案：在pom.xml中添加依赖
                ```
                <dependency>
                    <groupId>org.apache.tomcat</groupId>
                    <artifactId>jakartaee-migration</artifactId>
                    <version>1.0.0</version>
                </dependency>
                ```
        2. Error:(36, 26) java: 程序包aQute.bnd.annotation.spi不存在
            - 解决方案：在pom.xml中添加依赖
                ```
                <dependency>
                    <groupId>biz.aQute.bnd</groupId>
                    <artifactId>biz.aQute.bndlib</artifactId>
                    <version>5.3.0</version>
                    <scope>provided</scope>
                </dependency>
                ```
        3. Error:(299, 76) java: 找不到符号 符号:   变量 VERSION_9 位置: 类 org.eclipse.jdt.internal.compiler.impl.CompilerOptions
            - 原因：maven库的ecj版本太低，手动更新
            - 解决方案
                - pom.xml移除ecj
                - 手动下载ecj
                    1. ecj是eclipse的包，因此可以去eclipse网站下载
                    2. 进入网站：https://archive.eclipse.org/eclipse/downloads/
                    3. 找到需要的版本，这里选择4.21，打开下载页
                    ![](.\res\ecj_download_1.png)
                    4. 搜索ecj，点击下载ecj
                    ![](.\res\ecj_download_2.png)
                - 添加依赖
                    - 在源码中创建lib文件夹
                    - 将ecj-4.21.jar放入源码lib文件夹
                    ![](.\res\ecj_dir.png)
                    - idea手动添加依赖
                        - file -> project structure -> Library -> + -> java -> 选择lib文件夹中的ecj
                    ![](.\res\add_ecj.png)
    - 浏览器访问http://localhost:8080
        1. 报错：org.apache.jasper.JasperException: 无法为JSP编译类
            - 原因：直接启动Bootstrap类的时候没有加载JasperInitializer,因此无法编译JSP
            - 解决方案：在ContextConfig.configureStart函数中手动将JSP解析器初始化
                1. 找到org.apache.catalina.startup.ContextConfig类
                2. 在configureStart函数中初始化jsp解析器
                    - 在执行完webConfig函数后添加初始化代码
                        ```
                        webConfig();
                        // 初始化jsp解析器
                        context.addServletContainerInitializer(new JasperInitializer(), null);
                        ```
                    ![](.\res\add_code.png)


    



参考
1. [tomcat源码编译](https://www.cnblogs.com/zhaomo/p/12535872.html)
2. [tomcat源码运行](https://blog.csdn.net/weixin_36586120/article/details/117620726)