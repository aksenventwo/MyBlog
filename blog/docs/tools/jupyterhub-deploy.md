## Jupyterhub实战

这是一篇参照`Jupyterhub`官方文档而写的备忘，它主要用于已经阅读过`Jupyterhub`官方文档的人作为备忘录使用，也可作为下次部署的指导进行快速部署。

## 1. 安装环境准备

1. nodejs
2. Python3.4

### 1.1 安装 configurable http proxy

```shell
npm install -g configurable-http-proxy
```

### 1.2 安装 multi-user Hub 和 Jupyter notebook

```shell
pip3 install virtualenv
virtualenv -p python3 ~/py3env
source ~/py3env/bin/activiate
pip3 install jupyterhub
pip3 install jupyter
```

更详细的安装文档在Jupyterhub的官方文档上[Install](http://jupyterhub.readthedocs.io/en/latest/quickstart.html)

### 1.3 启动 jupyterhub 测试是否安装成功

```shell
jupyterhub
```

## 2. 使用配置文件

1. 生成默认配置文件
2. 指定 Admin 用户
3. 设置用户登录的文件夹路径
4. 设置本地用户登录
5. 使用可选的第三方认证登录


### 2.1 生成默认配置文件

生成的配置文件默认是 `jupyterhub_config.py`:

```shell
# 当前文件夹
jupyterhub --generate-config

# 指定文件夹
jupyterhub --generate-config /etc/jupyterhub/jupyterhub_config.py
```

#### 2.1.1 使用配置文件启动 `Jupyterhub`

```shell
jupyterhub -f /etc/jupyterhub/jupyterhub_config.py
```

#### 2.2 指定 Admin 用户

编辑配置文件：

```shell
vi /etc/jupyterhub/jupyterhub_config.py
```

在`vim`的非插入模式下键入`/`进行查找，如：

```
/admin_users
```

查找到如下内容，把注释取消，删除行首的`#`，如下填入你需要设置的用户名：

```
c.Authenticator.admin_users = {'aksenventwo', 'root'}
```

保存退出即可


#### 2.3 设置用户登录的文件夹路径

同样使用`vim`的查找功能，查找`notebook_dir`

```shell
c.Spawner.notebook_dir = '/home/aksenventwo/notebook'
```

#### 2.4 设置本地用户登录



#### 2.5 使用可选的第三方认证登录

`Jupyterhub`提供了很多第三方认证的登录服务，都需要配置，比较麻烦。现在以`Github`为例：

#### 2.5.1 安装验证插件、

```shell
pip3 install oauthenticator
```

#### 2.5.2 开启`Github`验证插件， 写入配置文件即可

```python
from oauthenticator.github import GitHubOAuthenticator
c.JupyterHub.authenticator_class = GitHubOAuthenticator
```

#### 2.5.3 在`Github`上注册一个`OAuth application`

地址在这 [Github OAuth](https://github.com/settings/applications/new)

最重要的是你的回调URL，符合如下格式：

```shell
http[s]://[your-host]/hub/oauth_callback
```

如我的`Jupyterhub`的项目地址是`jupyterhub.example.com:8000`，那么回调URL就是`http://jupyterhub.example.com:8000/hub/oauth_callback`


然后在你的环境中填入环境变量，这些值在你注册`OA`成功后`Github`会给你：

```
export GITHUB_CLIENT_ID=fffffffffff
export GITHUB_CLIENT_SECRET=fffffffffffffffffffff
export OAUTH_CALLBACK_URL=http://jupyterhub.example.com:8000/hub/oauth_callback
```

这三个值必须使用环境变量的方式设置，写入配置文件中不生效，亲测。

使用github登录的前提是在服务器上必须有同名的系统用户，否则登录会报500错误。


## 3. 常见问题

1. tornado 版本问题
2. configurable-http-proxy问题


### 3.1 tornado 版本问题

tornado 的版本应该跟随你的 jupyterhub 安装时的版本，在安装其他包时会如果有tornado依赖，应该注意它的版本

### 3.2 configurable-http-proxy 问题

当你启动jupyterhub时，遇到如下问题:

```
tornado.httpclient.HTTPError: HTTP 403: Forbidden
```

很简单，只是configurable-http-proxy已经在运行了，只需要杀掉即可

```
ps aux | grep configurable-http-proxy
```