ucsdk持续集成推送
====


一、UCSDK新版本推送
----

**uc_webview_sdk发布到阿里的maven仓库**

>仓库地址：http://repo.alibaba-inc.com/nexus/index.html?spm=0.0.0.0.wddb9L#nexus-search;quick~uc_sdk_webview
>
>发布地址：http://aone.alibaba-inc.com/aone2/myaone

*发布步骤：*
>新建变更
>
>填写GAV参数：
>
>>groupid:__com.taobao.android__
>>
>>artifactid:__uc_webview_sdk__
>>
>>version:__1.3.3__
>
>选择文件
>
>添加到待上传
 
*webview_core_libs.zip发布到cdn仓库*
>
>发布地址：http://mt.alibaba-inc.com/file/index
>

二、组件升级适配代码、使用新UCSDK版本、并生成调试包
----

**手淘windvane项目组件git仓库：** _git@gitlab.alibaba-inc.com:ali-wireless-h5/windvane-android.git_
>
*使用git clone克隆到本地之前，需要先在该项目主页的gitlab系统中按提示生成和添加ssh公钥*
>
>项目主页：*http://gitlab.alibaba-inc.com/ali-wireless-h5/windvane-android*
>
然后就可以用这个新建的ssh密钥进行克隆和提交了（密码在生成密钥时自己设定）。
 
**设置maven环境**
>
>*修改/etc/maven/settings.xml内容为*
>
>		<?xml version="1.0"?>
>		<!-- ======================================================================== -->
>		<!--  settings.xml for taobao maven users                                     -->
>		<!--  Version: 1                                                              -->
>		<!--  Auhtor: jingshun shi                                                    -->
>		<!--  $Id: settings.xml 182629 2013-06-07 14:31:35Z jingshun.shijs $ -->
>		<!-- ======================================================================== -->
>		 
>		<settings>
>		    <servers> 
>		        <server> 
>		            <id>snapshots</id> 
>		            <username>snapshotsAdmin</username> 
>		            <password>123456</password> 
>		        </server> 
>		        <server>
>		            <id>releases</id> 
>		                <username>admin</username> 
>		                <password>screct</password> 
>		        </server>
>		   </servers> 
>		 
>		        <!-- ======================================================================== -->
>		        <!--  mirror settings                                                         -->
>		        <!-- ======================================================================== -->
>		    <mirrors> 
>		        <mirror> 
>		            <id>tbmirror-all</id> 
>		            <mirrorOf>*</mirrorOf>  
>		            <name>taobao mirror</name>
>		            <url>http://mvnrepo.alibaba-inc.com/mvn/repository</url>
>		        </mirror> 
>		    </mirrors> 
>		        <!-- ======================================================================== -->
>		        <!--  Profiles                                                                -->
>		        <!-- ======================================================================== -->
>		    <profiles>
>		        <profile> 
>		                    <id>nexus</id> 
>		                    <repositories> 
>		                        <repository> 
>		                                <id>central</id> 
>		                                <url>http://mvnrepo.alibaba-inc.com/mvn/repository</url> 
>		                        </repository> 
>		                    </repositories> 
>		                    <pluginRepositories> 
>		                        <pluginRepository> 
>		                                <id>central</id> 
>		                                <url>http://mvnrepo.alibaba-inc.com/mvn/repository</url> 
>		                        </pluginRepository> 
>		                    </pluginRepositories> 
>		            </profile> 
>		    </profiles> 
>		 
>		        <pluginGroups>
>		                 <pluginGroup>com.alibaba.org.apache.maven.plugins</pluginGroup>
>		                         <pluginGroup>com.alibaba.maven.plugins</pluginGroup>
>		        </pluginGroups>
>		 
>		    <activeProfiles> 
>		        <activeProfile>nexus</activeProfile> 
>		    </activeProfiles> 
>		  
>		</settings> >		

 
 
**开发分支**
>
>*开发分支都基于develop开出，ucsdk应该有自己独立的开发分支。*
>

**使用mvn clean install进行本地编译**


*开发版本、编译和推送组件*
>
>_使用以下脚本修改本地代码（所生成组件）的版本号，注意到带SNAPSHOP关键字将能够覆盖（即不用升版本号）发布。_
>
>        ./version.php 6.3.0-uc-yourname-SNAPSHOP
>
>*使用mvn clean install deploy 进行编译好的组件发布，发布后，可以在以下仓库查看所有组件*
>
>        http://repo.alibaba-inc.com/nexus/index.html?spm=0.0.0.0.wddb9L#nexus-search;quick~windvane 
>

三、打包调试
----

**平台地址：**
>
>        http://mtl3.alibaba-inc.com/project/project_build_config.htm?spm=0.0.0.0.1JfeoL&projectId=20407&buildConfigId=120274
> 

**根据上一章得到的相关的组件描述：**
>        <dependency>
>          <groupId>com.taobao.android</groupId>
>          <artifactId>windvane</artifactId>
>          <version>6.3.0-uc-SNAPSHOT</version>
>        </dependency>
> 
*克隆配置以后，点击编辑配置，然后加上自己的版本依赖 比如要修改windvane的打包依赖版本，可以在打包配置后面加上*
>
>        -Dcom.taobao.android.windvane.version=6.3.0-SNAPSHOT
>

*参考地址：[Confluence_UCSDK]("http://doc.ucweb.local/pages/viewpage.action?pageId=78348673")*