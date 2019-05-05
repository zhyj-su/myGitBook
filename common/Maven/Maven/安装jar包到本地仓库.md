# 3-7 安装jar包到本地仓库

安装指定文件到本地仓库命令：mvn install:install-file
-DgroupId=<groupId>       : 设置项目代码的包名(一般用组织名)
-DartifactId=<artifactId> : 设置项目名或模块名 
-Dversion=1.0.0           : 版本号
-Dpackaging=jar           : 什么类型的文件(jar包)
-Dfile=<myfile.jar>       : 指定jar文件路径与文件名(同目录只需文件名)


安装命令实例：
mvn install:install-file -DgroupId=com.baidu -DartifactId=ueditor -Dversion=1.0.0 -Dpackaging=jar -Dfile=ueditor-1.1.2.jar

mvn deploy:deploy-file -DgroupId=com.linkage.fa -DartifactId=lsso -Dversion=3.1.0 -Dpackaging=jar -Dfile=com.linkage.fa.lsso-3.1.0.jar -Durl=http://10.254.2.95:8083/repository/internal/ -DrepositoryId=bc-hadoop