

准备一个springboot的项目

### 1.新建任务

![image-20200610121911362](img/new_a_project.png)

### 2.新建一个maven项目

![image-20200610121813980](img/new_maven_project.png)

### 3.项目配置

![image-20200610122112357](img/setting.png)

源码管理的[Credentials][Credentials]可以添加。添加输入你对应的github的账号密码
![image-20200610122228278](img/setting_2.png)

**构建触发器**

![image-20200610122228278](img/builder.png)

构建环境可以不用选

指定构建的pom.xml 以及构建操作clean install -DskipTests

![image-20200610123222977](img/build.png)

添加构建成功后的操作，选择要上传文件到服务器

![image-20200610123727940](img/build_post_steps_1.png)

![image-20200610123530842](img/build_post_steps.png)

执行sh的代码参考

```bash
DATE=$(date +%Y%m%d)
export JAVA_HOME PATH CLASSPATH
JAVA_HOME=/usr/local/lib64/jdk-14.0.1
PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$CLASSPATH
PROJECT_NAME=$1
DIR=/root/
JARFILE=$2

echo "project name:$PROJECT_NAME"
echo "JAR file : $JARFILE"

if [ ! -d $DIR/backup ];then
   mkdir -p $DIR/backup
fi
cd $DIR
## show_str=`ps -ef | grep "java -jar backup/$JARFILE" | grep 'java' | grep -v grep`
## echo "show_str $show_str"
## project_pids=`ps -ef | grep "$JARFILE" | grep 'java' | grep -v grep | awk '{print $2}'`
## echo "111project_pids ${project_pids[*]}"
## if [ ${#project_pids[*]} > 0 ];then
##    echo "项目已经启动了，开始关闭项..."
##    ps -ef | grep "$JARFILE" | grep 'java' | grep -v grep | awk '{print $2}' | xargs kill -9
##    echo "项目关闭成功,开始重启项目"
## else  
##    echo "项目未启动,直接启动"
## fi    
ps -ef | grep "java -jar backup/$JARFILE" | awk '{print $2}' | xargs kill -9
mv /jenkins-in/$JARFILE backup/$JARFILE
echo "try running"
java -jar backup/$JARFILE  > out.log &
if [ $? = 0 ];then
        sleep 30
        tail -n 50 out.log
fi
cd backup/
ls -lt|awk 'NR>5{print $NF}'|xargs rm -rf
```



[Credentials]:https://www.jenkins.io/zh/doc/book/using/using-credentials/	"Credentials"



