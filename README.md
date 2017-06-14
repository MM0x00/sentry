# sentry 在线实验环境

## 软件简介

Sentry是一个实时事件记录和聚合平台。它专门监控错误并提取完成正确的验尸所需的所有信息，而无需任何标准用户反馈回路的麻烦

所属类别是开发工具


## 软件官网

http://sentry.apache.org/

## Dockerfile 使用方法

### 如何使用
如何设置完整的Sentry实例

启动一个Redis容器
```
$ docker run -d --name sentry-redis redis
```
启动Postgres容器
```
$ docker run -d --name sentry-postgres -e POSTGRES_PASSWORD=secret -e POSTGRES_USER=sentry postgres
``` 
生成一个新的秘密密钥，以便所有sentry容器共享。该值将被用作SENTRY_SECRET_KEY环境变量。
```
$ docker run --rm sentry config generate-secret-key
```
如果这是一个新的数据库，你需要运行 upgrade
```
$ docker run -it --rm -e SENTRY_SECRET_KEY='<secret-key>' --link sentry-postgres:postgres --link sentry-redis:redis sentry upgrade
```
注意：这-it是重要的，因为初始升级将提示创建初始用户，如果没有它将失败

现在启动Sentry服务器
```
$ docker run -d --name my-sentry -e SENTRY_SECRET_KEY='<secret-key>' --link sentry-redis:redis --link sentry-postgres:postgres sentry
```
默认配置需要一个celery beat和celery workers，开始尽可能多的workers（每个都有一个唯一的名字）
```
$ docker run -d --name sentry-cron -e SENTRY_SECRET_KEY='<secret-key>' --link sentry-postgres:postgres --link sentry-redis:redis sentry run cron
$ docker run -d --name sentry-worker-1 -e SENTRY_SECRET_KEY='<secret-key>' --link sentry-postgres:postgres --link sentry-redis:redis sentry run worker
```
### 端口映射
如果您希望能够在没有容器的IP的情况下从主机访问实例，则可以使用标准端口映射。只需添加-p 8080:9000到docker run参数，然后访问或者http://localhost:8080或http://host-ip:8080在浏览器中。

## 资源链接

- https://github.com/getsentry/sentry
- https://www.oschina.net/p/sentry
- http://sentry.apache.org/
