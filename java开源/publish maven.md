### 1. 生成秘钥

```ruby
gpg --full-generate-key
```

连续敲三个空格后，输入`y`

记住`Passphase`，相当于一个密钥库的密码，用来鉴权是不是所有人在使用

最后保存起来，我一般保存到`${user.home}/.gnupg`，与秘钥存放位置放一起

这里注意一下，默认私钥和公钥会保存在`C:\Users\${user_name}\AppData\Roaming\gnupg\`目录中

而IDE会找到`${user.home}/.gnupg`该目录下，如果没有做任何操作，你生成的秘钥通过`gpg --list-keys`是找不到的

下面会详细给步骤

### 2. 查看秘钥

```ruby
gpg --list-keys
```

> 获取key Id 来获取秘钥可以为 公钥后16位和8位，推荐16位

### 3. 发布公钥到公钥服务器

```ruby
gpg --keyserver hkp://keyserver.ubuntu.com:11371 --send-keys FD953A355469C8AA
```

无提示、无报错即可

### 4. 从公钥服务器获取公钥

```ruby
gpg --keyserver hkp://keyserver.ubuntu.com:11371 --recv-keys FD953A355469C8AA
```

```ruby
gpg: key 12E2CADABE0D891E: "fyupeng <1160886967@qq.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
```

### 5. 获取公钥列表

```ruby
C:\Users\fyp01>gpg --list-keys
C:\Users\fyp01\AppData\Roaming\gnupg\pubring.kbx
------------------------------------------------
pub   ed25519 2022-08-20 [SC] [expires: 2024-08-19]
      76591F10A73649202490BA3812E2CADABE0D891E
uid           [ultimate] fyupeng <1160886967@qq.com>
sub   cv25519 2022-08-20 [E] [expires: 2024-08-19]
```

### 6. 打印公钥

```ruby
gpg --armor --export FD953A355469C8AA
```

### 7. 打印私钥

```ruby
gpg --list-secret-keys --keyid-format LONG
```

### 8. 发布异常

解决`maven-javadoc-plugin`报错 警告`warning`异常

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>2.9.1</version>
    <configuration>
        <aggregate>true</aggregate>
    </configuration>
    <executions>
        <execution>
            <id>attach-javadocs</id>
            <goals>
                <goal>jar</goal>
            </goals>
            <!-- 忽略警告异常 -->
            <configuration>
                <additionalOptions>-Xdoclint:none</additionalOptions>
            </configuration>
        </execution>
    </executions>
</plugin>
```

- 解决下面异常

```ruby
gpg: skipped "FD953A355469C8AA": No secret key
gpg: signing failed: No secret key
```

将路径`C:\Users\fyp01\AppData\Roaming\gnupg`下的公钥和私钥复制到`${user.home}/.gnupg`目录中

- 解决下面异常

```ruby
gpg: invalid size of lockfile '/c/Users/fyp01/.gnupg/pubring.kbx.lock'
gpg: cannot read lockfile
gpg: can't lock '/c/Users/fyp01/.gnupg/pubring.kbx'
/c/Users/fyp01/.gnupg/pubring.kbx
```

删除`${user.home}./gnupg`目录下的所有`.lock`文件即可，自动生成的

- 解决`status405`异常

也就是`put`失败，不支持`Method`的相关操作

确保`deploy`这个`SNATSHOT`测试版本后要先看是否校验通过，并且要去`close`，当然可以再继续`release`，否则不能发布正式版本

这样正式版本发布会找到已经发布了的测试版本，就给上传了

- 解决`status401`异常

身份未校验，建议在`build`和`plugin`中都加入gpg校验，否则会报错，并且分开测试和正式校验

```xml
<distributionManagement>
    <snapshotRepository>
        <!-- 指定为测试版本仓库 -->
        <id>sonatype-nexus-snapshots</id>
        <url>https://s01.oss.sonatype.org/content/repositories/snapshots/</url>
    </snapshotRepository>
    <!-- 指定为正式版本仓库 -->
    <repository>
        <id>sonatype-nexus-staging</id>
        <url>https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/</url>
    </repository>
</distributionManagement>
```

`maven`中`setting.xml`

```xml
<servers>
    <server>
        <id>sonatype-nexus-snapshots</id><!--名字表示用于快照库-->
        <username>${your_sonatype_username}</username>
        <password>${your_sonatype_password}</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id><!--名字表示用于发布库-->
        <username>${your_sonatype_username}</username>
        <password>${your_sonatype_password}</password>
    </server>
</servers>
```

而且发布正式要多一个依赖，主要用于发布，没有会报错

因为这个插件有属性`autoReleaseAfterClose`，在你手动关闭之后，再次发布会自动发布到相应的正式仓库中

```xml
<profiles>
    <profile>
        <id>release</id>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.sonatype.plugins</groupId>
                    <artifactId>nexus-staging-maven-plugin</artifactId>
                    <version>1.6.7</version>
                    <extensions>true</extensions>
                    <configuration>
                        <serverId>sonatype-nexus-staging</serverId>
                        <nexusUrl>https://s01.oss.sonatype.org/</nexusUrl>
                        <autoReleaseAfterClose>true</autoReleaseAfterClose>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```

### 9. 发布测试

修改`version`加一个`-SNAPSHOT`

```ruby
mvn clean deploy
```

### 10. 发布正式

修改`version` 不要加`-SNAPSHOT`

```ruby
mvn clean deploy -P release
```

可以手动修改，也可以执行

```ruby
mvn versions:set -DnewVersion=1.0.0
```

### 11. 完整`pom`文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.fyupeng</groupId>
    <artifactId>rpc-netty-framework</artifactId>
    <packaging>pom</packaging>
    <version>1.0.0</version>
	<!-- 没有这一项close版本时会校验项目名失败 -->
    <name>rpc-netty-framework</name>
    <!-- 没有这一项close版本时会校验项目链接失败 -->
    <url>https://github.com/fyupeng/rpc-netty-framework</url>
    <description>基于Netty分布式微服务的RPC框架</description>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <netty.version>4.1.20.Final</netty.version>
    </properties>

    <modules>
        <module>rpc-core</module>
        <module>rpc-common</module>
    </modules>

    <dependencies>
        <!-- 配置中心 nacos -->
        <dependency>
            <groupId>com.alibaba.nacos</groupId>
            <artifactId>nacos-client</artifactId>
            <version>1.4.3</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>


    <licenses>
        <license>
            <name>The MIT License</name>
            <url>https://github.com/fyupeng/rpc-netty-framework/blob/main/LICENCE</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <name>fyupeng</name>
            <email>fyp010311@126.com</email>
        </developer>
    </developers>

    <scm>
        <connection>scm:git:https://github.com/fyupeng/rpc-netty-framework.git</connection>
        <developerConnection>scm:git:https://github.com/fyupeng/rpc-netty-framework.git</developerConnection>
        <url>https://github.com/fyupeng/rpc-netty-framework</url>
    </scm>

    <distributionManagement>
        <snapshotRepository>
            <!-- 指定为测试版本仓库 -->
            <id>sonatype-nexus-snapshots</id>
            <url>https://s01.oss.sonatype.org/content/repositories/snapshots/</url>
        </snapshotRepository>
        <!-- 指定为正式版本仓库 -->
        <repository>
            <id>sonatype-nexus-staging</id>
            <url>https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/</url>
        </repository>
    </distributionManagement>


    <build>
        <finalName>rpc-netty-framework</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!--打包源代码-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!--生成javadoc-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-javadoc-plugin</artifactId>
                <executions>
                    <execution>
                        <id>attach-javadocs</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                        <!-- 忽略警告异常 -->
                        <configuration>
                            <additionalOptions>-Xdoclint:none</additionalOptions>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <!--获取本地私钥签名-->
            <!-- 发布测试版没有这一项 close 版本时会校验签名失败 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-gpg-plugin</artifactId>
                <version>1.5</version>
                <executions>
                    <execution>
                        <id>sign-artifacts</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>sign</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <profile>
            <id>jdk-18</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
            </activation>
            <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
            </properties>
        </profile>

        <profile>
            <id>release</id>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.sonatype.plugins</groupId>
                        <artifactId>nexus-staging-maven-plugin</artifactId>
                        <version>1.6.7</version>
                        <extensions>true</extensions>
                        <configuration>
                            <serverId>sonatype-nexus-staging</serverId>
                            <nexusUrl>https://s01.oss.sonatype.org/</nexusUrl>
                            <autoReleaseAfterClose>true</autoReleaseAfterClose>
                        </configuration>
                    </plugin>
                    <!-- 发布正式版没有这一项 close 版本时会校验签名失败 -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.5</version>
                        <executions>
                            <execution>
                                <id>sign-artifacts</id>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>

    </profiles>



</project>
```

`maven`目录中`setting.xml`配置

```xml
<servers>
    <server>
        <id>sonatype-nexus-snapshots</id><!--名字表示用于快照库-->
        <username>${your_sonatype_username}</username>
        <password>${your_sonatype_password}</password>
    </server>
    <server>
        <id>sonatype-nexus-staging</id><!--名字表示用于发布库-->
        <username>${your_sonatype_username}</username>
        <password>${your_sonatype_password}</password>
    </server>
</servers>
```

```xml
<profile>
        <id>ossrh</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <gpg.keyname>${your_gpg_keyId}</gpg.keyname>
          <gpg.executable>D:\software\GnuPG\bin\gpg</gpg.executable>
          <gpg.passphrase>${your_gpg_passphrase}</gpg.passphrase>
        </properties>
     </profile>
```

