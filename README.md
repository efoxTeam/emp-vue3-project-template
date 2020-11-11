# EMP Vue3 应用模板工程

## 依赖库 package.json

``` json
  "devDependencies": {
    "@efox/emp-cli": "^1.1.4",
    "@efox/emp-vue3": "^1.0.1",
    "@vue/compiler-sfc": "^3.0.0-rc.10",
    "vue-loader": "^16.0.0-beta.5"
  },
  "dependencies": {
    "vue": "^3.0.0-rc.10"
  },
```

## 微前端配置 emp-config.js

``` javascript
const withVue3 = require('@efox/emp-vue3')
const path = require('path')
const ProjectRootPath = path.resolve('./')
const { getConfig } = require(path.join(ProjectRootPath, './src/config'))
module.exports = withVue3(({ config, env, empEnv }) => {
  const confEnv = env === 'production' ? 'prod' : 'dev'
  const conf = getConfig(empEnv || confEnv)
  const port = conf.port
  const projectName = 'vue3Project'
  const publicPath = conf.publicPath
  // 设置项目URL
  config.output.publicPath(publicPath)
  // 设置项目端口
  config.devServer.port(port)
  config.plugin('mf').tap(args => {
    args[0] = {
      ...args[0],
      ...{
        // 项目名称
        name: projectName,
        // 暴露项目的全局变量名
        library: { type: 'var', name: projectName },
        // 被远程引入的文件名
        filename: 'emp.js',
        remotes: {
          // 远程项目别名:远程引入的项目名
          vue3Components: 'vue3Components',
        },
        // 需要暴露的东西
        exposes: {
          // 别名:组件的路径
        },
      },
    }
    return args
  })
  // 配置 index.html
  config.plugin('html').tap(args => {
    args[0] = {
      ...args[0],
      ...{
        // head 的 title
        title: 'EMP Vue3 Project',
        // 远程调用项目的文件链接
        files: {
          js: [conf.baseRemoteEntry],
        },
      },
    }
    return args
  })
})
```
