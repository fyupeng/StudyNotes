`loback` 日志配置

```java
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--  日志输出文件路径配置  -->
    <property name="log.path" value="D:/spring-boot-rnf/server/logs" />
    <!--  日志输出控制台  -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!--%date{HH:mm:ss.SSS} %c -->
            <pattern>%date{HH:mm:ss.SSS} [%level] %c [%t] - %m%n</pattern>
        </encoder>
    </appender>

    <!-- 日志彩色输出控制台   -->
    <!-- 引入spirng boot默认的logback配置文件 -->
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- 采用Spring boot中默认的控制台彩色日志输出模板 -->
        <encoder>
            <pattern>${CONSOLE_LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <!--输出到文件-->
    <!-- 时间滚动输出 level为 INFO 日志 -->
    <appender name="INFO_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 正在记录的日志文件的路径及文件名 -->
        <file>${log.path}/log_info.log</file>
        <!--日志文件输出格式-->
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <!-- 日志记录器的滚动策略，按日期，按大小记录 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 每天日志归档路径以及格式 -->
            <fileNamePattern>${log.path}/info/log-info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!--日志文件保留天数-->
            <maxHistory>15</maxHistory>
        </rollingPolicy>
        <!-- 此日志文件只记录info级别的 -->
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <root level="info">
        <appender-ref ref="STDOUT"/>
<!--        <appender-ref ref="INFO_FILE"/>-->
    </root>
</configuration>
```

