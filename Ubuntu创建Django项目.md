## 安装虚拟环境：
    ---安装库virtualenv: pip install virtualenv
    ---安装库virtualenvwrapper: 
        pip install virtualenvwrapper(下载可能会出错)
        sudo pip install-i https://pypi.tuna.tsinghua.edu.cn/simple --no-deps virtualenvwrapper(镜像下载)
    ---修改根目录.bashrc配置文件：
        vim .bashrc
        最后添加内容: export WORKON_HOME=$HOME/.virtualenvs
                     source /usr/local/bin/virtualenvwrapper.sh
        source ~/.bashrc
    ---创建python3虚拟环境：
        mkvirtualenv Django_python -p python3
    ---激活虚拟环境：
        workon Django_python
    ---虚拟环境安装Django包
        pip install Django==3.2 
        (3.2为版本号)
    
## 创建Django项目：
    cd到想要创建项目的目录：django-admin startproject Django_test

## 启动服务器查看Django项目是否创建成功：
    python manage.py runserver

## Django项目创建App：
    python manage.py startapp App

## 创建模板项目文件夹，创建index.html模板文件

## 主文件夹下的urls.py下添加目录：
    from django.contrib import admin
    from django.urls import path
    from App import views

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('index/',views.index)
    ]

## App文件夹下views.py写入对应的方法：
    def index(request):
        return render(request,'index.html')

## 主文件夹下settings.py template目录添加
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [
                os.path.join(BASE_DIR,'templates')
            ],
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]

## APP.views.py调用html模板构建视图函数：
    from django.shortcuts import render
    def index(request):
        return render(request, 'index.html')

## 子路由使用：
    ---在APP（APP名称为Two）中创建自己的urls
        from django.urls import path
        from Two import views

        urlpatterns=[
            path(r'index/',views.index)
        ]
    ---根路由规则
        urlpatterns = [
            path(r'two/',include('Two.urls'))
            ]

## 创建表
    ---在APP的models.py中定义模型
        <!-- student为表名 -->
        class student(models.Model)  
            s_name=models.CharField(max_length=20)
            s_age=models.IntegerField(default=1)
    ---模型迁移
        python manage.py makemigrations
    ---将迁移文件映射到库中
        python manage.py migrate
    此时已生成sqlite的表，pycharm可以通过database查看，vscode需要安装相应的扩展插件

## 表中插入数据
    ---APP的urls.py中添加路由规则：
        path(r'addstudent/',views.addstudent)
    ---APP的views.py构建视图函数
        from Two.models import student
            def addstudent(request):
                stu = student()
                stu.s_name='Jerry'
                stu.save()
                return HttpResponse("Add success")


## 数据查询
    ---urls.py添加路径
        path(r'getstudents/',views.getstudent)
    ---views.py构建数据查询函数：
        def getstudent(request):
            students = student.objects.all()
            for stu in students:
                print(stu.s_name)
            # return HttpResponse("student List")
            context={
                'hobby':'play games',
                'students':students
            }
            return render(request,'student.html',context=context)
    ---html模板语法：
        <!DOCTYPE html>
        <html lang="zh">
        <head>
            <meta charset="UTF-8">
            <meta http-equiv="X-UA-Compatible" content="IE=edge">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>StudentList</title>
        </head>
        <body>
            <h2>Ubuntu</h2>
            <h3>{{hobby}}</h3>
            <h1>Student List</h1>
            <hr>
            <ul>
                
                {% for stu in students %}
                    <li>{{stu.s_name}}</li>
                {% endfor %}
        
            </ul>
        </body>
        </html>

## 数据更新
    ---ulrs.py添加路径
        path(r'updatestudent/',views.updatestudent)
    ---view.py构建视图函数
        def updatestudent(requset):
            stu=student.objects.get(pk=1)
            stu.s_name='Jack'
            stu.save() 
            return HttpResponse('student update success')
    ---数据库中查看更新是否生效

## 数据删除
    ---ulrs.py添加路径
        path(r'deletestudent/',views.deletestudent)
    ---view.py构建视图函数
        def deletestudent(requset):
            stu=student.objects.get(pk=3)
            stu.delete()
            return HttpResponse('student delete success')
    ---数据库中查看更新是否生效	