---
title: Jenkins-2021春招准备
date: 2021-03-23 11:33:03
tags:
---



#### Jenkins

> Jenkins使用主从架构来管理分布式构建。在这种体系架构中，主机和从机通过TCP/IP协议进行通信。

##### 架构

![title](/images/2021年春招准备-jenkins/1.png)

- Jenkins 主机
  - 调度构建工作，将构建分派给从服务器进行实际构建
  - 监视从机
  - 记录并显示构建结果
  - Jenkins的主机也可以直接执行构建

- Jenkins 从机
  - 配置不同的环境
  - 侦听来自Jenkins 主机的请求
  - Jenkins 从机可以运行在各种操作系统上
  - Jenkins 从机的工作是执行命令，这包括执行由主机分派的构建任务
  - 可以为项目配置执行构建的从机。

**主从连接**

- **使用SSH方法：**使用ssh协议连接到从服务器。连接从Jenkins主站启动。这应该是主机和从机之间通过端口22的连接。

- **使用JNLP方法：**使用Java JNLP协议。在这种方法中，Java代理从带有Jenkins主详细信息的从属启动。为此，主节点防火墙应允许指定JNLP端口上的连接。通常，分配的端口为50000。此值是可配置的

**Jenkins Slave有两种类型**

- **从节点：**这些是将配置为静态**从节点**的服务器（Windows / Linux）。这些从站将一直处于运行状态，并保持与Jenkins服务器的连接
- **从属云：** Jenkins Cloud从属是具有动态Slave的概念。意味着，每当您触发作业时，从属将按需部署为VM /容器，并在作业完成后被删除。当您拥有庞大的Jenkins生态系统并持续构建时，此方法可节省基础设施成本。



##### Jenkinsfile

```groovy
#!/groovy
pipeline{
	agent any

	environment {
		REPOSITORY="https://gitee.com/MrZCoding/dop.git"
		SERVICE_DIR="user-server"
		DOCKER_REGISTRY_HOST="registry.dop.clsaa.com"
		DOCKER_REGISTRY="registry.dop.clsaa.com/dop/user-server"
	}

	stages {
		stage('pull code') {
			steps {
				echo "start fetch code from git:${REPOSITORY}"
				deleteDir()
				git "${REPOSITORY}"
                script {
                    time = sh(returnStdout: true, script: 'date "+%Y%m%d%H%M"').trim()
                    git_version = sh(returnStdout: true, script: 'git log -1 --pretty=format:"%h"').trim()
                    build_tag = time+git_version
                }
			}
		}

		stage('build maven') {
			steps {
                echo "star compile"
                dir(SERVICE_DIR){
                    sh "ls -l"
                    sh "mvn -U -am clean package"
                }
			}
		}

		stage('build docker') {
			steps {
                echo "start build image"
                echo "image tag : ${build_tag}"
                dir(SERVICE_DIR){
                    sh "ls -l"
                    sh "docker build -t ${DOCKER_REGISTRY}:${build_tag} ."
                }
			}
		}

       stage('push docker') {
            steps {
                echo "start push image"
                dir(SERVICE_DIR){
                  sh "ls -l"
                  withCredentials([usernamePassword(credentialsId: 'docker_registry', passwordVariable: 'docker_registryPassword', usernameVariable: 'docker_registryUsername')]) {
                      sh "docker login -u ${docker_registryUsername} -p ${docker_registryPassword} ${DOCKER_REGISTRY_HOST}"
                      sh "docker push ${DOCKER_REGISTRY}:${build_tag}"
                  }
                }
            }
        }

        stage('update yaml') {
            steps{
                echo "start change yaml image tag"
                dir(SERVICE_DIR){
                    sh "ls -l"
                    sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
                    sh "cat k8s.yaml"
                }
            }
        }

		stage('deploy') {
			steps {
				echo "start deploy"
				dir(SERVICE_DIR){
				    sh "ls -l"
				    sh "kubectl apply -f k8s.yaml"
				}
			}
		}
	}
}
```



##### 需要深挖一下的

https://blog.windanchaos.tech/2020/03/09/jenkins%E6%9E%B6%E6%9E%84%E5%92%8C%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/