# Lightweight REST API framework built on Express

[![Node-Fashion](https://img.shields.io/npm/v/node-fashion.svg)](https://www.npmjs.org/package/node-fashion) [![NPM downloads](http://img.shields.io/npm/dm/node-fashion.svg)](https://npmjs.org/package/node-fashion)

## Install
```bash
npm install --save node-fashion
```
or use node-fashion-seed
```bash
git clone https://github.com/anguer/node-fashion-seed.git fashion-demo
```

## Usage
users.js
```js
module.exports = [
  {
    url: '/users',
    method: 'get',
    handle: function (req, res) {
      res.respond(null, [
        {
          id: 123,
          name: 'sdx'
        },
        {
          id: 234,
          name: 'dss'
        }
      ])
    }
  },
  {
    url: '/users/:id',
    method: 'get',
    handle: function (req, res) {
      res.respond(null, 'get users by id.')
    }
  },
  {
    url: '/users',
    method: 'post',
    handle: function (req, res) {
      res.respond(null, 'add a user.')
    }
  }
]
```
api.js
```js
module.exports = [
  require('./users'),
  {
    url: '/hello-world',
    method: 'get',
    handle: function (req, res) {
      res.send('hello world.')
    }
  }
]
```
index.js
```js
var Server = require('node-fashion')

var server = new Server()

// add middleware
server.use(function (req, res, next) {
  console.log('============[配置中间件]=============')
  next()
})

// add basic api
server.use('/hello', function (req, res) {
  console.log('create api by [/hello].')
  res.send('test success.')
})

// 统一接口函数处理
server.handle(require('./api'))

server.start()
```
 
## Options
 - baseUrl: [String] - 默认`/api`
 - port: [Number] - 端口, 默认`12321`
 - debugger: [Boolean] - 调试模式, 默认`false`
 - logger: [Object] - 日志输出, 默认`console`
 
## Documents
 - route
   - url: 请求地址, 如'/users', '/users/:id'
   - method: HTTP请求方法, [OPTIONS|GET|POST|PUT|DELETE|...]
   - description: 描述信息
   - handle: 请求处理函数
     ```
     {
       url: '/users',
       method: 'GET',
       description: '获取用户列表',
       handle: function (req, res) {
         ...
         res.respond(null, [
           {id: 1, name: 'qwe'},
           {id: 2, name: 'aaa'},
         ])
       }
     }
     ```
     
 - server.handle([Array | Object])
   - 注册route, 可以传递一个route对象或route对象的数组
   
 - server.use()
   - 该方法等同于Express的use方法
   
 - server.start()
   - start a server
   
 - server.interceptors([Callback Function])
   - 简易路由拦截器, 携带四个参数req, res, route, next,
   - 其中route包含url,baseUrl,method,description四个属性
     ```
     server.interceptors(function (req, res, route, next) {
       console.log(route)
       next()
     })
     ```
   
 - server.beforeResponse([Callback Function])
   - 服务器响应客户端前的自定义回调函数, 你可以在这里做一些额外的工作, 默认`null`
   - 必须调用'res.respond()'方法, 该回调函数才会生效
   - 该回调接受三个参数(err, route, done)
     - err: 服务器处理返回的错误信息
     - route: 所请求的路由信息, 额外包含(params, body, originalUrl)信息
     - done: 必须调用`done()`方法来结束该回调函数
   
 - res.respond([Error Object | String], [Error Code | Object])
 ```
   // 调用通用响应处理函数
   function (req, res) {
     // ...
     // error - errorCode默认是`500`
     res.respond(new Error('error message'))
     res.respond(new Error('error message'), 401)
     res.respond('error message', 401)
     // success
     res.respond(null, [{id: 1, name: 'qwe'}, {id: 2, name: 'aaa'}])
   }
 ```

## Author
 - [Anguer](https://github.com/anguer)
