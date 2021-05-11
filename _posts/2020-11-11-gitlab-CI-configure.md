---
layout: post
title: "修正版Gitlab CI/CD配置"
date:  2020-11-11 12:51:40 +0800
typora-root-url: ..
categories: gitlab
---

* content
{:toc}

cyberrange项目cr-vroute子系统gitlab CI/CD配置说明。

## 概览

### 架构图

```mermaid
graph LR;
	Repo-- clone repo, by gitlab internal --> gitlab_runner;

	gitlab_runner-- clone repo, private ssh key by manual configure --> vm01;
	
	gitlab_runner-- clone repo, private ssh key by manual configure --> vm02;
	
	gitlab_runner-- clone repo, private ssh key by manual configure --> vm03;
	
	gitlab_runner-- clone repo, private ssh key by manual configure --> vm04;
	
```

图示解释：

1. gitlab runner从repo下拉代码。进行构建，测试等操作，其权限来自与runner配置时使用的token，不需要额外配置，只要正常注册了gitlab runner就可以，由gitlab帮我们搞定；
2. gitlab runner通过sshkey 来访问production_env进行部署代码。需要登录到production_env，使用的ssh private key，需要提前手动设置，生成ssh key pair，将private key保存到gitlab CI/CD设置的secret变量中，public key通过ssh-copy-id上传到production_env；
3. production_env访问repo下拉代码。需要提前手动配置ssh key，这不仅是为了部署时使用，紧急需要时，可以登录production_env手动下拉代码。ssh key配置和用户在gitlab或者github上传ssh key是一样的。

关于为什么要单独设置gitlab-runner：

1. 便于项目的迁移，只需要调整少部分runner的设置即可以重新复现整个项目。
2. 对于一些企业和公司，runner大多是作为单独的服务被出租的，也就意味着开发者只能接触到自己的开发环境，而不需要关心runner和git之间的互动，减少开发的工作量。

但是这样同样也会带来一些不便利的地方，例如在添加生产环境之前需要单独配置生产服务器所需的环境。

### 部署环境

* gitlab-runner

  | 环境       | 描述                                                         |
  | ---------- | ------------------------------------------------------------ |
  | `操作系统` | ubuntu                                                       |
  | `python`   | 3.5                                                          |
  | `docker`   | image:<br />   gitlab-runner:lastest<br />   localhost:5000/ |

* production_env

  | 环境                 | 描述               |
  | -------------------- | ------------------ |
  | `操作系统`           | ubuntu 14.04.5 LTS |
  | `python`s            | 3.5                |
  | `virtualenv`         |                    |
  | `openvswitch-switch` | 2.0.2              |

### 账号和IP

* gitlab-runner

  | 环境      | 描述       |
  | --------- | ---------- |
  | `IP`      | 10.10.20.3 |
  | `ACCOUNT` | ubuntu     |

* production_env

  | 环境      | 描述              |
  | --------- | ----------------- |
  | `IP`      | 192.168.120.11-14 |
  | `ACCOUNT` | ubuntu            |
  | PASSWD    | crnetworksys      |

## gitlab-runner<->repo

### install gitlab-runner

#### install from apt

```bash
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
apt-get install gitlab-runner
```

#### install from docker

1. 修改apt的源：

```
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```

2. Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:

```bash
$ su
$ apt-get -y update
$ apt-get -y upgrade
$ apt-get -y install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

3. Add Docker’s official GPG key:

```bash
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

Verify that you now have the key with the fingerprint`9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.

```bash
$ apt-key fingerprint 0EBFCD88
/etc/apt/trusted.gpg
--------------------
pub   1024D/437D05B5 2004-09-12
      Key fingerprint = 6302 39CC 130E 1A7F D81A  27B1 4097 6EAF 437D 05B5
uid                  Ubuntu Archive Automatic Signing Key <ftpmaster@ubuntu.com>
sub   2048g/79164387 2004-09-12

pub   1024D/FBB75451 2004-12-30
      Key fingerprint = C598 6B4F 1257 FFA8 6632  CBA7 4618 1433 FBB7 5451
uid                  Ubuntu CD Image Automatic Signing Key <cdimage@ubuntu.com>

pub   4096R/C0B21F32 2012-05-11
      Key fingerprint = 790B C727 7767 219C 42C8  6F93 3B4F E6AC C0B2 1F32
uid                  Ubuntu Archive Automatic Signing Key (2012) <ftpmaster@ubuntu.com>

pub   4096R/EFE21092 2012-05-11
      Key fingerprint = 8439 38DF 228D 22F7 B374  2BC0 D94A A3F0 EFE2 1092
uid                  Ubuntu CD Image Automatic Signing Key (2012) <cdimage@ubuntu.com>

pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

4. Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word `nightly` or `test` (or both) after the word `stable` in the commands below. [Learn about **nightly** and **test** channels](https://docs.docker.com/engine/install/).

```bash
$ add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

在尝试了几次之后发现这一步骤对ubuntu14.04.6并不适用，将源更换为阿里源后成功

```bash
$ add-apt-repository \
   "deb [arch=amd64]  
   http://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

5. install docker engine

```bash
 $ apt-get -y update
 $ apt-get -y install docker-ce
 $ service docker start
```

### setup gitlab-runner

``` bash
# pull image
$ docker pull gitlab/gitlab-runner
Using default tag: latest
latest: Pulling from gitlab/gitlab-runner
d72e567cc804: Pull complete
0f3630e5ff08: Pull complete
b6a83d81d1f4: Pull complete
795a1e0f662d: Pull complete
e26343354a23: Pull complete
df2906232f5a: Pull complete
e9423d1eea92: Pull complete
45a5486ea8e3: Pull complete
Digest: sha256:07d7c6d82b11646aa048427fadd9a1c0e5faa1efcea7e06689eadcf9b52da006
Status: Downloaded newer image for gitlab/gitlab-runner:latest
```

### register gitlab-runner

* register gitlab-runner using docker

 参考：[configure gitlab-runner](https://docs.gitlab.com/runner/register/index.html#docker)

``` bash
$ docker run --rm -t -i --add-host="gitlab.cr.net:192.168.102.25" -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=6 revision=fa86510e version=11.9.2
Running in system-mode.                            
                                                   
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
http://gitlab.cr.net/
Please enter the gitlab-ci token for this runner:
$PROJECT_REGISTRATION_TOKEN
Please enter the gitlab-ci description for this runner:
[a902a1cd5b80]: django-test
Please enter the gitlab-ci tags for this runner (comma separated):
django, test
```

或者使用命令行注册
```bash
$ PROJECT_REGISTRATION_TOKEN="MbagsxLmj9pYugNaiQdL"
$ docker run --rm -t -i --add-host="gitlab.cr.net:192.168.102.25" -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image python:3.4 \
  --url "http://gitlab.cr.net/" \
  --registration-token $PROJECT_REGISTRATION_TOKEN \
  --description "vm-04" \
  --tag-list "crvroute" \
  --run-untagged="true" \
  --locked="true" \
  --docker-tlsverify ="true" \
  --docker-extra-hosts "gitlab.cr.net:192.168.102.25"
```

* register gitlab-runner

直接使用gitlab-runner注册

```bash
$ PROJECT_REGISTRATION_TOKEN="MbagsxLmj9pYugNaiQdL"
$ gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image python:3.4 \
  --url "http://gitlab.cr.net/" \
  --registration-token $PROJECT_REGISTRATION_TOKEN \
  --description "vm-04" \
  --tag-list "crvroute" \
  --run-untagged="true" \
  --locked="true" \
  --docker-tlsverify ="true" \
  --docker-extra-hosts "gitlab.cr.net:192.168.102.25"
```

更多配置请参考文档：     [advanced configure](https://docs.gitlab.com/runner/configuration/advanced-configuration.html)

### run gitlab-runner

运行docker中的gitlab-runner镜像

``` bash
# run to check it's ok
$ docker run -d --name gitlab-runner --restart always \
   --add-host=gitlab.cr.net:192.168.102.25 \
   -v /srv/gitlab-runner/config:/etc/gitlab-runner \
   -v /var/run/docker.sock:/var/run/docker.sock \
   gitlab/gitlab-runner:latest
```

或者直接启动gitlab-runner服务

```bash
$ gitlab-runner start
```

### 自定义docker 镜像

由于runner需要与生产环境之间进行通信，所以需要openssl、rsync等应用软件，而由于网络的原因，配置runner所需的环境会花费很长的时间，所以可以自己制作docker镜像，将这些依赖都准备好，加快测试和部署的速度。

#### Dockerfile

Dockerfile


``` Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.4

# Copy the current directory contents into the container at /app
COPY requirements.txt /requirements.txt

# Install any needed packages specified in requirement.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Update
RUN apt-get update -y && \
  apt-get install -y expect, openssh-client, rsync && \
  apt-get install -y software-properties-common && \
  apt-get install -y openvswitch-switch && \
  apt-get autoremove -y && \
  apt-get clean

# Make port 80 available to the world outside this container
# EXPOSE 80

# Define environment variable
# ENV NAME World

# Run app.py when the container launches
# CMD ["python", "app.py"]

```
```bash
$ docker build -t crsys-gitlab-runner:2.0 .
$ docker image tag crsys-gitlab-runner:2.0 192.168.120.11:5002/crsys-gitlab-runner:2.0
$ docker push 192.168.120.11:5002/crsys-gitlab-runner:2.0
```

change .gitlab-ci.yml

#### docker commit

启动一个docker容器并进入该容器

```bash
$ docker run -d -it --privileged --name production python:3.5
$ docker exec -it eb789ffd636f /bin/bash
```

配置production容器的依赖环境

```bash
root@eb789ffd636f:/# cp /etc/apt/sources.list /etc/apt/sources.list_bak
root@eb789ffd636f:/# sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
root@eb789ffd636f:/# apt-get -y update
root@eb789ffd636f:/# apt-get install -y openvswitch-switch
root@eb789ffd636f:/# mkdir -p /usr/local/etc/openvswitch
root@eb789ffd636f:/# mkdir -p /var/run/openvswitch/
root@eb789ffd636f:/# ovsdb-tool create usr/local/etc/openvswitch/conf.db /usr/share/openvswitch/vswitch.ovsschema
root@eb789ffd636f:/# ovsdb-server /usr/local/etc/openvswitch/conf.db --remote=punix:/var/run/openvswitch/db.sock --pidfile --detach
root@eb789ffd636f:/# ovs-vsctl --no-wait init
root@eb789ffd636f:/# ovs-vswitchd --pidfile --detach --log-file
root@eb789ffd636f:/# pip install virtualenv
root@eb789ffd636f:/# apt-get install -y expect openssh-client
root@eb789ffd636f:/# apt-get install -y software-properties-common
root@eb789ffd636f:/# apt-get autoremove -y
root@eb789ffd636f:/# apt-get clean
```

打包该镜像

```bash
$ docker commit -a "wmh" -m "production env" 容器名称或id crsys-gitlab-runner:3.0
$ docker image tag crsys-gitlab-runner:3.0 192.168.120.11:5002/crsys-gitlab-runner:3.0
$ docker push 192.168.120.11:5002/crsys-gitlab-runner:3.0
```

### runner变量

gitlab的变量配置如下：

![image-20201112202853609](/img/2020-11-11-gitlab-CI-configure/image-20201112202853609.png)

* deploy_key_file：存放生产环境中用来访问gitlab的公钥文件路径。
* flask_dir：flask app所在的路径
* git_repo_src：git仓库地址
* prod_env_passwd：生产环境的sudo密码
* production_dir：生产环境存放从git上拉取的文件路径
* ssh_private_key：gitlab-runner免密登录生产环境所需的私钥
* virtualenv_dirname：虚拟环境的名称

## gitlab-runner <-> production_env

gitlab-runner的管道中可以存在多个task，一般默认为test build deploy。在这些阶段中设置runner与production通信，下发相关命令到生产环境中实现项目的部署。

### ansible

ansible是新出现的自动化运维工具，基于Python开发，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能，Ansible默认通过 SSH 协议管理机器。

ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。主要包括：

(1)、连接插件connection plugins：负责和被监控端实现通信；

(2)、host inventory：指定操作的主机，是一个配置文件里面定义监控的主机；

(3)、各种模块核心模块、command模块、自定义模块；

(4)、借助于插件完成记录日志邮件等功能；

(5)、playbook：剧本执行多个任务时，非必需可以让节点一次性运行多个任务。

### ansible-playbook

Playbooks 与 adhoc 相比,是一种完全不同的运用 ansible 的方式.

简单来说,playbooks 是一种简单的配置管理系统与多机器部署系统的基础.与现有的其他系统有不同之处,且非常适合于复杂应用的部署.

Playbooks 可用于声明配置,更强大的地方在于,在 playbooks 中可以编排有序的执行过程,甚至于做到在多组机器间,来回有序的执行特别指定的步骤.并且可以同步或异步的发起任务.

我们使用 adhoc 时,主要是使用 /usr/bin/ansible 程序执行任务.而使用 playbooks 时,更多是将之放入源码控制之中,用之推送你的配置或是用于确认你的远程系统的配置是否符合配置规范.

Playbooks 的格式是YAML（详见:[*YAML 语法*](http://www.ansible.com.cn/docs/YAMLSyntax.html)）,语法做到最小化,意在避免 playbooks 成为一种编程语言或是脚本,但它也并不是一个配置模型或过程的模型.

playbook 由一个或多个 ‘plays’ 组成.它的内容是一个以 ‘plays’ 为元素的列表.

在 play 之中,一组机器被映射为定义好的角色.在 ansible 中,play 的内容,被称为 tasks,即任务.在基本层次的应用中,一个任务是一个对 ansible 模块的调用.

配置文件的代码如下所示，可以分为三个阶段：

1. 拉取代码

   设置生产环境从gitlab-runner中拉取代码，避免runner和production_env之间的文件传输。

2. 安装虚拟环境

   将项目所需的应用软件安装到虚拟环境中，注意虚拟环境中的pip版本可能和实际的生产环境中的有所不同。

3. 启动项目

   创建项目所需的日志和运行文件夹：/var/log/cr-vroute /var/run/cr-vroute。

   调整权限后重启服务。

```python
- name: "pull code from {{ git_repo_src }} to {{ production_dir }}"
  git:
    repo: "{{ git_repo_src }}"
    dest: "{{ production_dir }}"
    version: "{{ version }}"
    clone: yes
    accept_hostkey: yes
    key_file: "{{ deploy_key_file }}"
    force: true

- name: install python requirements to virtualenv
  become: yes
  become_method: sudo
  pip: 
    virtualenv: "{{ flask_dir }}/{{ virtualenv_dirname }}/"
    virtualenv_python: /usr/bin/python3
    requirements: "{{ flask_dir }}/requirements.txt"
    extra_args: "-i https://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn"

- name: create log dictionary if it does not exists
  become: yes
  become_method: sudo
  file:
    path: /var/log/cr-vroute
    state: directory
    mode: '0755'
  tags: dictionary

- name: create pid dictionary if it does not exists
  become: yes
  become_method: sudo
  file:
    path: /var/run/cr-vroute
    state: directory
    mode: '0755'
  tags: dictionary

- name: set service scripts mode
  file:
    path: "{{ flask_dir }}/bin/cr-vroute"
    state: touch
    mode: '0755'

- name: stop service cr-vroute
  become: yes
  become_method: sudo
  shell: "{{ flask_dir }}/bin/cr-vroute stop"

- name: start service cr-vroute
  become: yes
  become_method: sudo
  shell: "{{ flask_dir }}/bin/cr-vroute start"
```

### key pair

利用ssh的密钥对实现gitlab-runner和生产环境之间的免密登录。首先生成一对秘钥，将公钥发送到ubuntu@ADDR_VM01，并在gitlab CI/CD配置中添加secret变量：

* 变量名为 SSH_PRIVATE_KEY，内容填写输出的私钥

```bash
root@eb789ffd636f:/# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:jBNpEw4YHXCax+eu6JU6KOakMs+F9h7LGZSGavq2IuI root@baseimage
The key's randomart image is:
+---[RSA 2048]----+
|  o=+..          |
|  .=.o o         |
|  o o B          |
|   o = =         |
|  . + + S        |
| . + o .         |
|.+o * .          |
|&+oB *           |
|@EO+B            |
+----[SHA256]-----+
root@baseimage:/# ssh-copy-id -i ~/.ssh/id_rsa username@$production_env
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
nsr@192.168.xxx.xxx's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'username@192.168.xxx.xxx'"
and check to make sure that only the key(s) you wanted were added.
root@baseimage:/# cat ~/.ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxx0Dt1ryLXVHBkPuvra3l85nLeDPRKLyR00Z8XR/aFv8v0vF
ZJecQ289n6aySm+uJ9gXUWfeDx/PuRGTgoa8ZDNew9I2+NKiwOKVFu7tsnCU6B2b
nenL8ScO6sk9NcJZuNBZITeuK/LAC7pa9fl39ixdWPBoe4n8hRa2nEWmrPek7v6I
mtRIDPUgKhLrqogmWWTT4j2r3Gr/UjgnIEbkq3LIUQTJIdkZLQmDi9s+H9Kct+NB
ndnRBFHjuR5MN1gTtfXT7HkphNCyJzF+IGn7VsJsY1f7wZaSlGKZn3NnejBT3G76
KYsH5UJcmvEckM4nZjX26DQEh5ihHw+utzIK5QIDAQABAoIBAQCqtNJp0XwNwx1Y
XseCFG7EpL3LFJE+vCDRRmX2n7VXyH2hmBmsy1q6adXAWY9DyAncuSQMs+sqtlBF
z67S2icMFQqrAkAFBblpexjtJ3z8Xg8cDb5MHZSutJjSbCOhQnXfwCiaFP2rShyi
MWwotG/Hy3NgfARn3f50L+vxgLmMUeNwdl6rvNkyHhI9/uto4+i+2kgrhv0n1YuB
18/5MQd8G9kYjvrHxhlqqLPvKawCjNVDoUv6s4MDjjkNTkooPV5CkCbisrRyENQm
+1Jv6e3pm/JCx/CVGJCLA32f/q2fWrREsVyFGo4/vxJxMk7Uu1GLIVgq9HVbp8hR
BPrJGoLlAoGBAPscvNZTNAMjslrbKubUEqCGRIQjk9y/DuYPkHk6M5+olM3GD2YY
0s5AtuquzzZ5JnyOigGwHwrQs75b8SgxfsF2T/iKKt09wB6ayNRfioT3BXFzIIVj
VPyHU/VcdyL30QJYBlEg1PFQPh59RgV5Az5fujET64q/5H4bdGUb7CEjAoGBAMr9
LCdtvbBfy0hKOx0ADSHnSY8N4tOy9I6hmI7cndbVCzK7lQV5eh/atlt269bmCCOj
7lcGKrjBhRyBGJE6c9tPGJuUWBRZ4p3pAKuPp650Nue1MvfiChoqN4DgOmSxuBIV
c/oZwqJpMrAVdGEKRONRo4UTMBpkA1eVp5lsAphXAoGAfboEvlaISy4bRaxITaxO
6zpuoa5igPrUDdIrJtdCM6UnY+PPn/G70nLKYrRFoWOFJoYDiRbSZArcg+nq05H8
gganwawEDCO3xlr/RH3Vj/9SJVxcIwz55zomiknSiJqFdmP2PbZvh6cCgyd74I+X
VSp+wcXUf/LWB2HdpCYX0vsCgYEAxMwjbK6Wg1K8UP+2w+zyznXf+RNrX/dV5pqA
v7DgThIKY0s9tSsK3as7tP1ctOIb7gHW5wQRk0H7faEjYlmbTXonz15I7qIedR4b
CjmiRh5hxM8F9RFu3eMkIIxD0LAEV3P5Fva2nqMDA3gpdpNwKjtSH4h00IE6REXR
UYtIEM0CgYEA18yX+rzOOSS/V/ut8ajRUUuxFnUAbMIg+3wk2lkrt2pA/5QmQI/7
IVxCHmtzYiMFK/lNqwadg75B2Syw8/nQI5YU6E94b3S+l1rzGoItYdRIQsdWL/iL
VryMAG1U4bce34bs5pKA4WFiXmbcD/OECguYARj2zSQjvg+o6j3FTDo=
-----END RSA PRIVATE KEY-----
```

### 配置生产环境

```bash
ubuntu@vRoute:/# sudo apt-get install -y openvswitch-switch
ubuntu@vRoute:/# sudo mkdir -p /usr/local/etc/openvswitch
ubuntu@vRoute:/# sudo mkdir -p /var/run/openvswitch/
ubuntu@vRoute:/# sudo ovsdb-tool create usr/local/etc/openvswitch/conf.db /usr/share/openvswitch/vswitch.ovsschema
ubuntu@vRoute:/# sudo ovsdb-server /usr/local/etc/openvswitch/conf.db --remote=punix:/var/run/openvswitch/db.sock --pidfile --detach
ubuntu@vRoute:/# sudo ovs-vsctl --no-wait init
ubuntu@vRoute:/# sudo ovs-vswitchd --pidfile --detach --log-file
ubuntu@vRoute:/# sudo pip install virtualenv
```

## production_env -> repo

同上生成ssh key pair,不同的是在生产环境系统下操作，之后将公钥上传到gitlab。

```bash
ubuntu@production_env:/# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:DCCgzLEbQDzgMO03qsLyCeMlH/rnOU31oF6Vt3icG+E root@production_env
The key's randomart image is:
+---[RSA 2048]----+
|X=. .            |
|B++. .           |
|.B.   .     .    |
|  + o  o o o o   |
| . o .  S + = +  |
|  .    o . o E   |
|+o o  + .   . o  |
|=+=..o.o     .   |
|o++ooo.          |
+----[SHA256]-----+

```

至此，就实现了repo、gitlab-runner和production_env之间的互相通信。

## Trouble shooting

### docker is not active

![image-20201104160836058](/img/2020-11-11-gitlab-CI-configure/image-20201104160836058.png)

这是因为镜像没有启动

### docker pull error

![image-20201105203130082](/img/2020-11-11-gitlab-CI-configure/image-20201105203130082.png)

因为docker在之后的版本中默认使用了https设置，导致协议不匹配所以出现了错误

编辑/etc/default/docker，在其中添加:

```bash
DOCKER_OPTS="--insecure-registry=192.168.120.11:5002"
```

重启docker后发现仍然存在错误：

![image-20201105204840077](/img/2020-11-11-gitlab-CI-configure/image-20201105204840077.png)

是因为5000端口已经被uwsgi占用，因此docker无法解析该应用回复的响应。

```bash
docker pull registry:2
docker run -d -p 5002:5000 --restart=always registry:2
```

运行成功之后使用curl http://localhost:5002却出现connect refused的错误，经过对端口进行检查之后发现

![image-20201106094918602](/img/2020-11-11-gitlab-CI-configure/image-20201106094918602.png)

修改ubuntu的网络设置，禁用tcp6。打开/etc/sysctl.conf，添加如下三条设置

```bash
   net.ipv6.conf.all.disable_ipv6 = 1
   net.ipv6.conf.default.disable_ipv6 = 1
   net.ipv6.conf.lo.disable_ipv6 = 1
```

保存后退出，执行sudo sysctl -p。经过测试后发现不是这个原因，可能是因为宿主机和容器端口映射设置错误，将启动容器的端口设置修改为-p 5002:5000后可以成功访问http://localhost:5002。

为了使gitlab-runner中的docker可以接受http协议，在启动gitlab-runner容器的时候设定tls_vertify=true

### 本地调试

![image-20201106102748598](/img/2020-11-11-gitlab-CI-configure/image-20201106102748598.png)

一直push到gitlab上太麻烦了，所以使用gitlab-runner exec进行本地调试

```bash
 $ gitlab-runner exec docker test_staging 
```

在docker的镜像中进行本地调试会出现很多错误：

* 无法正确clone

修改.gitlab.yml，添加如下内容：

```yaml
variables:
	GIT_STRATEGY: none
```

* docker无法启动

启动docker时设置--privilege 

### cd导致的权限问题

![image-20201109000402307](/img/2020-11-11-gitlab-CI-configure/image-20201109000402307.png)

在gitlab-runner中使用了cd [dictionary]会导致文件夹只读。
