### container,image

```bash
# 将容器提交成镜像
# docker commit [commond] 原容器名|ID 保存的镜像名:版本号
docker commit -a "hxh" -m "update something" nginx01 mynginx:0.1

# 将镜像打tar包
# docker save -o 保存的文件名 原镜像名|ID
docker save -o ./mynginx.tar mynginx:0.1
 
# 将压缩包加载成镜像
docker load -i ./mynginx.tar

#-----------------------------------
# 将镜像推送到远程仓库
# 1.现在 dockerhub中创建个人仓库 mynginx,创建完成后提示 docker push xiaohui24/mynginx:tagname

# 2.给本地镜像打个标签
docker tag mynginx:0.1 xiaohui24/mynginx:0.1
 
# 3.推送打完标签后的本地镜像,需要登录认证
docker login # 按提示输入 用户名 密码  # docker logout 退出
docker push xiaohui24/mynginx:0.1  # 推送完成后可以在dockerhub 搜索 xiaohui24/mynginx

# 4.运行自己推送的镜像
docker run -d -p 8080:80 --name mynginx02 xiaohui24/mynginx:0.1
```



### volume

```bash
# 挂载数据卷,如果宿主机没有~/logs文件夹则自动创建
docker run -dP -v ~/logs/tomcatlog:/usr/local/tomcat/logs --name=tomcat02 tomcat:jre8-alpine

#-----------------------

# 创建具名数据卷,tomcat03
docker run -dP -v tomcat03:/usr/local/tomcat/logs --name=tomcat03 tomcat:jre8-alpine
# 查看具名数据卷的信息
docker volume inspect tomcat03

#-----------------------

# 创建匿名数据卷
docker run -dP -v usr/local/tomcat/logs --name=tomcat02 tomcat:jre8-alpine
# 数据卷在宿主机的位置
docker inspect tomcat02
# 清除无用数据卷
docker volume prune
```





### logs

```bash
# docker logs -f 容器ID
docker logs -f 20cc

# 复制容器的文件到宿主机
# docker cp 容器ID:容    文件路径
docker cp 20cc:/etc/nginx/nginx.conf ~/nginx.conf
```



### Dockerfile

```dockerfile
FROM openjdk:17-alpine
LABEL maintainer="linux.hxh@outlook.com"
COPY ./app.jar /apps/app.jar
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai'>/etc/timezone && touch /apps/app.jar
EXPOSE 8080
ENV JAVA_OPTIONS=""
ENV PARAMS=""
ENTRYPOINT ["sh","-c","java -Djava.security.egd=file:/dev/./urandom $JAVA_OPTIONS -jar /apps/app.jar $PARAMS"]
```



```dockerfile
FROM maven:3.8.5-openjdk-17-slim AS buildapp
WORKDIR /apps
COPY src .
COPY pom.xml .
RUN mvn clean package -Dmaven.test.skip=true
RUN cp /apps/target/*.jar /apps/app.jar

FROM openjdk:17-alpine
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai'>/etc/timezone
LABEL maintainer="linux.hxh@outlook.com"
COPY --from=buildapp /apps/app.jar /app.jar
ENV JAVA_OPTIONS=""
ENV PARAMS=""

ENTRYPOINT ["sh","-c","java -Djava.security.egd=file:/dev/./urandom $JAVA_OPTIONS -jar /app.jar $PARAMS"]
```



### network

```bash
# 创建自定义网络 --subnet 定义子网范围
docker network create --subnet=192.169.0.0/16 mynet

# 查看自定义网络信息
docker network inspect mynet

# 多个容器使用同一个自定义网络,可以通过容器名 ping 通,使用自定义网络时,需要先创建自定义网络
docker run -dP --name=tomcat01 --network=mynet tomcat:jre8-alpine
docker run -dP --name=tomcat02 --network=mynet tomcat:jre8-alpine

#------------------------

# 将其他网络的容器添加到自定义的网络mynet中,方便tomcat04与自定义网络mynet  的其他容器通信
docker run -dP --name=tomcat04 tomcat:jre8-alpine
# 相当于给处于其他网络的容器Tomcat04添加了一个虚拟的网卡,分配了自定义网络mynet的ip
docker network connect mynet tomcat04
```



###   docker-compose

```bash
# 安装
sudo curl -SL https://github.com/docker/compose/releases/download/v2.24.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

 chmod +x /usr/local/bin/docker-compose

# 查看版本
docker-compose version
```



#### [compose示例](https://docs.docker.com/compose/gettingstarted/)

```bash
# 在此文件夹创建文件
mkdir composetest
cd composetest
```

1. app.py

```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

2. Dockerfile

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . . 
CMD ["flask", "run"]
```

3. requirements.txt

```text
flask
redis
```

4. compose.yaml

```yaml
version: "3.9" # compose与docker版本对应 https://docs.docker.com/compose/compose-file/compose-file-v3/
services:
  web:
    build: .
    image: app:1.0
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```

```bash
# 启动应用
docker-compose -d up

# 停止应用
docker-compose stop

# 停止并移除应用
docker-compose down
```



### example:

#### jenkins

```bash
docker run -u root -d -p 8080:8080 -p 50000:50000 --restart=always --name=jenkins01 -v jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean

# jenkins初始化过程可能遇到插件下载失败的问题
https://blog.csdn.net/weixin_44772835/article/details/129384897
# 插件源
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```

#### Jenkinsfile

```Jenkinsfile
pipeline {

    agent any

    // 配置环境变量
    environment {
      // 提前定义好系统默认的工作空间
      // 因为某个阶段使用临时的镜像会改变默认的工作空间
      WS = "${WORKSPACE}"

      // 配置个人的镜像仓库秘钥
      // 在jenkins web后台凭据管理里面添加
      // huawei_docker_repository 个人的凭据ID,全局唯一
      // 会自动生成 ...CREDENTIALS_USR . ..CREDENTIALS_PSW 分别表示账号密码
      IMAGE_VERSION = "1.0"
    }

    stages {
        stage('check') {
            steps {
                sh "java -version"
                sh "git --version"
                sh "docker version"
            }
        }

    stage('compile') {
        // 需要提前docker-pipeline插件
            agent {
                docker {
                // 相当于再宿主机创建一个新的容器
                image 'maven:3-alpine'
                // 相当于在宿主机 执行 docker run -v /apps/maven/repository:/root/.m2/repository...
                args "-v /apps/maven/repository:/root/.m2/repository"
             }
        }
        steps {
            echo "编译阶段"
            sh 'mvn --version'
            // 凡是需要取变量值的时候,都用双引号
            sh "echo $WS"
            // 每一行命令都是基于默认工作空间,此时默认空间已经被改变
            // 所以 cd .. mvn clean ... 不能分成两条命令写
            sh "cd ${WS} && mvn clean package -s '/var/jenkins_home/appconfig/maven/settings.xml' -Dmaven.test.skip=true"
            sh "pwd &&ls -alh"
        }
    }


    stage('build-image') {
        steps {
            sh "docker version"
            sh "pwd && ls -alh"
            // 删除未使用镜像
            sh "docker image prune -f"
            sh "docker build -t app:test ."
        }
    }


    stage("push-image") {
        steps {
            sh "docker login -u cn-east-3@LG4EUG4853LX2YIAMQ6R -p b39742ddbd80213d5c67ed354232aa530ad477f268c7e82b7afdf42d88553e15 swr.cn-east-3.myhuaweicloud.com"
            sh "docker tag app:test swr.cn-east-3.myhuaweicloud.com/hxh/app:${IMAGE_VERSION}"
            sh "docker push swr.cn-east-3.myhuaweicloud.com/hxh/app:${IMAGE_VERSION}"
            echo "推送镜像到华为云docker镜像仓库成功"
        }
    }

    stage('deploy-dev') {
        steps {
            sh "docker run -d -p 8088:8080 --name=app01 app:test"
            echo "部署成功"
        }
     }

     stage('deploy-product') {
         input {
             message "需要部署到生产环境中吗?"
             ok "是的,确认部署到生产环境"
         parameters {
             string(name: "IMAGE_VERSION", defaultValue: '1.0', description: '生产版本号')
             choice choices: ['北京', '上海', '长沙'], name: 'DEPLOY_AREA'
            }
        }

         steps {
            // groovy
            script{
                // 取出上面定义的变量 ,需要加双引号 "${DEPLOY_AREA}"
                def area = "${DEPLOY_AREA}"
                if (area == "北京") {
                    sh "echo 已经部署到北京地区"
                }
                else if (area == "上海") {
                    sh "echo 已经部署到上海地区"
                }
                else{
                    sh "echo 已经部署到长沙地区 "
                }
            }
         }

       }


    stage('report') {
         steps {
             echo "推送邮件报告"
             // 邮件html模板
             emailext body: '''<!DOCTYPE html>
             <html>
             <head>
             <meta charset="UTF-8">
             <title>${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志</title>
             </head>
             <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"
             offset="0">
                 <table width="95%" cellpadding="0" cellspacing="0"  style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">
                 <tr>
                     本邮件由系统自动发出，无需回复！<br/>
                     各位同事，大家好，以下为${PROJECT_NAME }项目构建信息</br>
                     <td><font color="#CC0000">构建结果 - ${BUILD_STATUS}</font></td>
                 </tr>
                 <tr>
                     <td><br />
                     <b><font color="#0B610B">构建信息</font></b>
                     <hr size="2" width="100%" align="center" /></td>
                 </tr>
                 <tr>
                     <td>
                         <ul>
                             <li>项目名称 ： ${PROJECT_NAME}</li>
                             <li>项目描述 ： ${JOB_DESCRIPTION}</li>
                             <li>构建编号 ： 第${BUILD_NUMBER}次构建</li>
                             <li>触发原因： ${CAUSE}</li>
                             <li>构建状态： ${BUILD_STATUS}</li>
                             <li>构建日志： <a href="${BUILD_URL}console">${BUILD_URL}console</a></li>
                             <li>构建  Url ： <a href="${BUILD_URL}">${BUILD_URL}</a></li>
                             <li>工作目录 ： <a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>
                             <li>项目  Url ： <a href="${PROJECT_URL}">${PROJECT_URL}</a></li>
                         </ul>
                     </td>
                 </tr>
                 <tr>
                     <td><b><font color="#0B610B">变更集</font></b>
                     <hr size="2" width="100%" align="center" /></td>
                 </tr>
                     <td>${JELLY_SCRIPT,template="html"}<br/>
                         <hr size="2" width="100%" align="center" /></td>
                     </tr>
                 </table>
             </body>
             </html>
             ''', subject: ' ${ENV, var="JOB_NAME"} -第${BUILD_NUMBER}次构建日志', to: 'linux.hxh@outlook.com'

             echo "流水线报告邮件已发送"
         }
      }
   }

}
```

