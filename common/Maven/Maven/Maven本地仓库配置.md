# 2-2 本地仓库配置

### 配置maven本地仓库

1. 拷贝本地仓库

   拷贝maven本地仓库，解压到非中文、非空目录

2. 配置本地仓库

   打开maven安装目录conf/settings.xml文件

   搜索local

   ```xml
     <!-- localRepository
      | The path to the local repository maven will use to store artifacts.
      |
      | Default: ${user.home}/.m2/repository
     <localRepository>/path/to/local/repo</localRepository>
     -->
   <localRepository>D:\develop\Maven\repository_ssh</localRepository>
   ```

将maven本地仓库路径配置一下即可