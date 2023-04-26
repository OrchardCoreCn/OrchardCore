# 使用Docker与Orchard Core

我们的源代码仓库包含一个 `Dockerfile`，可以让你创建自己的Docker镜像和容器。当Orchard Core开发人员需要测试PR时，这非常有用。它允许他们快速部署本地一些测试环境。这里的例子将展示如何在这种情况下使用Docker。 Docker也可以用于更复杂的用途（如：生产部署），但本文档并不旨在详细解释这些内容。对于更高级的示例，我强烈建议阅读 `docker` 和 `docker-compose` 文档。

*或者，对于使用Nuget包解决方案的人们；您可以直接从源代码复制Dockerfile和.dockerignore文件到您的解决方案的根文件夹中，以执行相同的操作。但是，这可能会变得复杂，具体取决于您是否使用了与源代码解决方案不同的文件夹结构。*

## 你需要什么

对于Windows用户：https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers

对于Ubuntu / Linux用户：https://docs.docker.com/engine/install/ubuntu/

## 你将要构建什么

你将使用 `docker` 和 `docker-compose` 命令从命令行构建Docker镜像和容器。图像是从Orchard Core源代码构建的，针对特定操作系统。然后我们可以从它们部署"容器"。这允许您查看Orchard Core在不同环境下的响应方式，或者在生产服务器上部署Orchard Core。

## Dockerfile

Orchard Core源代码中提供的Dockerfile使用中间镜像在包含.NET SDK的特定环境中构建Orchard Core。然后，我们使用仅使用ASP.NET Core运行时的方式创建“真正的”镜像。
```dockerfile
# 使用 .NET Core SDK 创建一个临时镜像
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-env
LABEL stage=build-env
WORKDIR /app

# 在临时镜像中复制并构建应用程序
COPY ./src /app
RUN dotnet publish /app/OrchardCore.Cms.Web -c Release -o ./build/release

# 构建运行时镜像
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
EXPOSE 80
ENV ASPNETCORE_URLS http://+:80
WORKDIR /app
COPY --from=build-env /app/build/release .
ENTRYPOINT ["dotnet", "OrchardCore.Cms.Web.dll"]
``` 


忽略Docker文件 ".dockerignore"

```yml
# 忽略所有
**

# 除了用于构建的src
!./src/*

# 忽略任何App_Data文件夹
**/App_Data/

# 忽略所有预构建文件
**/[b|B]in/
**/[O|o]bj/
```

## Docker


第一个例子很简单。使用Docker构建镜像并运行它（在容器内）。

````cmd
REM Dockerfile所在的文件夹
cd /orchardcore

REM 从Dockerfile构建映像
docker build -t oc .

REM 创建容器，运行它并将其服务暴露在端口80上
docker run -p 80:80 oc
````

## 删除中间镜像

### 当使用 `docker` 命令时：
```cmd
REM 删除构建过程中创建的中间容器，使用 --rm 参数
docker build -t oc --rm .

REM 删除所有中间镜像
docker image prune -f --filter label=stage=build-env

REM 创建一个容器，运行它，并将其服务暴露在 80 端口上
docker run -p 80:80 oc

```



使用这些命令将使您得到一个在端口80上完全运行的Docker容器，这样您就可以通过浏览器只需访问http://localhost就能访问到它。不过，我们假设这将仅允许您使用SQLite。为了避免需要直接在Docker主机计算机上安装任何东西并快速启动所有东西，您应该使用`docker-compose`。

## Docker Compose

Docker Compose 允许您通过在Orchard Core源代码的根文件夹中执行`docker-compose up`命令来生成多个本地容器。当然，首先需要有一个docker-compose.yml文件。在下面示例中，我们将为Orchard Core“官方支持”的每个数据库提供程序创建服务。

[Docker Compose 文档](https://docs.docker.com/compose/)

docker-compose.yml 文件示例:

```YML
version: '3.3'
services:
    web:
        build: 
            context: .
            dockerfile: Dockerfile
        ports:
            - "5009:80"
        depends_on:
            - sqlserver
            - mysql
            - postgresql
    sqlserver:
        image: "mcr.microsoft.com/mssql/server"
        environment:
            SA_PASSWORD: "P@ssw0rd!123456"
            ACCEPT_EULA: "Y"
    mysql:
        image: mysql:latest
        restart: always
        environment:
            MYSQL_DATABASE: 'orchardcore_database'
            MYSQL_USER: 'orchardcore_user'
            MYSQL_PASSWORD: 'orchardcore_password'
            MYSQL_ROOT_PASSWORD: 'root_password'
        ports:
            - '3306:3306'
        expose:
            - '3306'
        volumes:
            - mysql-data:/var/lib/mysql
    postgresql:
        image: postgres:latest
        volumes:
            - postgresql-data:/var/lib/postgresql/data
        ports:
            - 5432:5432
        environment:
            POSTGRES_USER: orchardcore_user
            POSTGRES_PASSWORD: orchardcore_password
            POSTGRES_DB: orchardcore_database
volumes:
    mysql-data:
    postgresql-data:

```

该文档为Docker Compose文件，其中定义了两个服务，一个为MySQL数据库服务，另一个为PostgreSQL数据库服务。MySQL服务将其默认端口3306映射到主机的3306端口，并暴露了该端口。通过定义的MySQL配置创建一个名为"orchardcore_database"的数据库，并创建名为"orchardcore_user"的用户。此外，该服务也定义了MYSQL_ROOT_PASSWORD环境变量作为root用户的密码，并将数据保存在名为“mysql-data”的数据卷中。 PostgreSQL服务同样进行了类似的操作，将默认端口5432映射到主机的5432端口，并暴露了该端口。它通过定义的POSTGRES_USER和POSTGRES_PASSWORD环境变量创建具有给定用户名和密码的PostgreSQL数据库用户，并将数据保存在名为“postgresql-data”的数据卷中。
这是一个使用Docker和Docker Compose构建应用程序的示例。该应用程序使用MySQL和PostgreSQL数据库，并创建两个数据卷以保存它们的数据。

在构建images之前，使用`docker-compose build`命令来构建images（如果它们尚未构建）。

然后，使用`docker image prune`命令来清除使用标签“stage = build-env”标记的构建环境中的中间image。

最后，使用`docker-compose up`命令启动所有的容器。
我们在`Dockerfile`中添加了一些清理中间图像的命令示例，因为我们的`Dockerfile`使用中间图像对我们的源代码进行`dotnet publish`。 如果不清理中间图像，则随着时间的推移，它肯定会占用大量磁盘空间。 我们使用`stage = build-env`标记这些中间图像，以便可以轻松地对其进行清理。

```cmd
REM Builds images if they are not already built
docker-compose build

REM Prune intermediate remaining images
docker image prune -f --filter label=stage=build-env

REM Start all containers
docker-compose up
```




## 自动创建租户（自动设置功能）

待办事项

请参阅：

[数据库 shell 配置](../../reference/core/Shells/README.md#database-shells-configuration-provider)

[Autosetup 功能 PR](https://github.com/OrchardCMS/OrchardCore/pull/4567)

## 如何在HTTPS上运行我的Orchard Core Docker容器？

[使用Docker托管ASP.NET Core映像并支持HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/host-aspnetcore-https.md)

## 如何针对特定环境定位我的Docker映像？

在此，您可以找到不同环境的列表，每个环境都有一个Dockerfile示例。
## 为什么Orchard Core源代码使用不同的Dockerfile？

### Dockerfile-CI、Dockerfile-CI.gitignore

Github Actions是我们用来构建和测试存储库中不同分支的持续集成工具。它可以让我们创建Docker镜像和容器，但在它们上构建Orchard Core比在实际CI上构建要慢。因此，对于这个问题，我们不在CI上使用中间镜像进行构建。不过，在本地进行这种构建完全没有性能限制，这完全是有意义的。

## 我可以使用不同的Dockerfile吗？

您需要使用至少Docker版本19.03才能使用Docker Buildkit，以便它可以解析不同的.dockerignore文件。

参见：

[使用BuildKit构建镜像](https://docs.docker.com/develop/develop-images/build_enhancements/)  
[Docker BuildKit是什么以及我能用它做什么？](https://brianchristner.io/what-is-docker-buildkit/#:~:text=Docker%20BuildKit%20is%20a%20little,and%20increase%20productivity%20for%20free.)  

Github Actions目前仅在Linux下支持Buildkit。
请查看：

https://github.com/docker/setup-buildx-action#limitation  
https://github.com/OrchardCMS/OrchardCore/issues/7651


> 该文档由Chat-GPT 翻译
