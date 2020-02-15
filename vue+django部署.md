## <font color=#B5402E>部署前的准备</font>
<hr style="border:3 double #987cb9" color=#987cb9 SIZE=3>
<br>
<br>

在开发完django后端接口和vue前端页面之后，我们的代码并不能直接放到远程服务器上去部署，需要一些准备，下面是在项目已经在本地对好接口，可以运行的情况下的部署准备操作
<br>
<br>

#### <font color=#B5402E>前端vue项目准备</font>
<hr width="90%">

1.页面开发完成，修改main.js文件中的baseURL，将原本的方便开发调试的localhost修改为远程服务器的公网地址，如图：

![image.png](https://www.yelin.site/b17187924fff11ea893400163e0c563d.png)

2.为了去掉vue项目生成后url中的#号，需要在router文件夹中的index.js文件中，在导出(export)的部分中加上：

```
mode: 'history',
```

如图：
![image.png](https://www.yelin.site/952123f44fff11ea893400163e0c563d.png)

3.做好上面的准备后，在vue项目所在的目录下打开cmd窗口，输入： 

```
npm run build
```

将项目打包为一个dist文件夹

![image.png](https://www.yelin.site/49ff9c28500111ea893400163e0c563d.png)
<br>
<br>

#### <font color=#B5402E>后端项目准备</font>
<hr width="90%">

1.在django项目中，先关掉Debug模式，将ALLOWED_HOSTS设置为['*'],方便所有网址访问:

![image.png](https://www.yelin.site/09a4626c500111ea893400163e0c563d.png)

2.在pycharm控制台或者本地路劲的cmd窗口输入：

```
pip freeze > requirements.txt
```

将这个项目所需的依赖包目录存进requirements.txt文件中

3.将数据库的用户名、密码等修改为服务器的mysql数据库名（如果服务器没有安装数据库，先安装，再配置）
<br>
<br>

#### <font color=#B5402E>linux服务器部署准备</font>
<hr width="90%">

1.在linux服务器中，创建一个文件夹，来保存整个项目，我创建的是一个/home文件夹。

完成后，在/home文件夹中创建三个文件夹

code: 用来存储项目的所有代码

conf: 用来存储这个项目所需的配置文件

logs: 用来存放产生的日志文件

在/home文件夹中创建pythn虚拟环境：

```
python3 -m venv venv
```

后一个venv为虚拟环境名称，并激活虚拟环境

```
source venv/bin/activate
```

2.开启linux中的mysql数据库，在数据库中创建django settings.py文件中所配置的数据库名，将vue项目准备时生成的dist文件夹和后端整个项目传过来，放在code文件夹中，去到code文件夹的django项目目录，使用命令行迁移文件：

```
python manage.py makemigrations
python manage.py migrate
```

3.同样在django项目目录下，安装项目所需的依赖包：

```
pip install -r reuqirements.txt
```

此时可以使用如下命令检查是否安装成功

```
pip list
```

4.安装uwsgi：

```
pip install uwsgi
```

此时所有的准备工作完成
<br>
<br>

## <font color=#B5402E>uwsgi配置文件</font>
<hr style="border:3 double #987cb9" color=#987cb9 SIZE=3>
<br>
<br>

1.去到刚刚创建的conf文件夹中，创建一个uwsgi.ini文件，vim打开该文件，进行如下配置

![image.png](https://www.yelin.site/8fad7c18500b11ea893400163e0c563d.png)

2.配置文件书写完成后，使用命令运行uwsgi(使用ini配置文件运行):
```
uwsgi --ini uwsgi.ini
```
<br>
<br>

## <font color=#B5402E>nginx配置文件</font>
<hr style="border:3 double #987cb9" color=#987cb9 SIZE=3>
<br>
<br>

1.同样在conf文件夹中创建nginx.conf文件，进行如下配置：

![image.png](https://www.yelin.site/c61e6734500c11ea893400163e0c563d.png)

2.在/etc/nginx/nginx.conf文件中配置总的nginx文件：

![image.png](https://www.yelin.site/25125946501011ea893400163e0c563d.png)

3.此时启动nginx,在浏览器中访问即可

```
systemctl start nginx
```
此时，vue+django的http就已经完成了。
<br>
<br>

## <font color=#B5402E>HTTPS部署</font>
<hr style="border:3 double #987cb9" color=#987cb9 SIZE=3>
<br>
<br>

在http部署完成以后，我们可以在浏览器上对项目进行访问，但是我们所有的接口、cookie等传输均为http不安全传输，也不被chrome、firefox等主流浏览器所认同，显示为非安全连接。

我们需要将所有的请求和连接都设置为https连接，保证文件传输的可靠性。
<br>
<br>

#### <font color=#B5402E>ssl证书准备：示例为阿里云服务器</font>
<hr width="90%">

1.在阿里云官网控制台上，购买好对应的ssl证书，在ssl证书控制台下载对应证书到本地：

![image.png](https://www.yelin.site/61a89308500f11ea893400163e0c563d.png)

2.在本地解压后出现两个文件：分别为.key, .pem文件

此时在/etc/nginx/目录下创建一个名为cert的文件夹，将证书解压后的两个文件夹放到cert文件夹下
<br>
<br>

#### <font color=#B5402E>vue代码修改</font>
<hr width="90%">

将main.js中的baseURL修改如下：

![image.png](https://www.yelin.site/36e8102e501111ea893400163e0c563d.png)
<br>
<br>

#### <font color=#B5402E>Django配置修改</font>
<hr width="90%">

打开django项目的settings.py文件，在配置文件的末尾加上如下配置：

![image.png](https://www.yelin.site/9031e38c501411ea893400163e0c563d.png)

<br>
<br>

#### <font color=#B5402E>nginx配置文件修改</font>
<hr width="90%">

去到conf文件夹中，打开nginx.conf文件，在文件中做如下修改：

![image.png](https://www.yelin.site/2f146f8e501411ea893400163e0c563d.png)


现在一切准备就绪，重启uwsgi和nginx服务器即可。

<br>
<br>

<font color=#B5402E>
--------------------------------------------------------------------------------------------
<br>
本文章为博主亲测，如有bug，请联系我，谢谢！
<br>
--------------------------------------------------------------------------------------------
</font>










