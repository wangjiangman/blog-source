
# express 4.x install

全局安装 express 

```
npm install -g express
```

全局安装 express generator

```
npm install -g express-generator
```

使用 ejs 作为模板生成项目

```
express -e project_name
```

启动项目

```
set DEBUG=project_name & npm start
```

或者 直接 npm start

或者使用 supervisor 启动， supervisor 会监视代码并自动启动服务器

```
npm install -g supervisor
```

启动 

```
supervisor .\bin\www
```
