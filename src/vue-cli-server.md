闲来无事，研究了下vue-cli-server。发现环境变量--mode 可以设置对应的环境变量

看了下vue-cli-service 的源码，发现只调用service.run方法

```
const rawArgv = process.argv.slice(2); // [ '--mode', 'dev' ]
// 会将 cross-env NODE_ENV=production vue-cli-service build --mode dev 命令解析成  {_: [], mode: 'dev'}

const args = require('minimist')(rawArgv, {
  boolean: [
    // build
    'modern',
    'report',
    'report-json',
    'inline-vue',
    'watch',
    // serve
    'open',
    'copy',
    'https',
    // inspect
    'verbose'
  ]
})
// 打印 {
  _: [],
  modern: false,
  report: false,
  'report-json': false,
  'inline-vue': false,
  watch: false,
  open: false,
  copy: false,
  https: false,
  verbose: false,
  mode: 'dev'
}
```

```
// cli-server 中调用loadEnv 两次。
// .env 全局默认配置文件，不论什么环境都会加载合并
// .env.dev 是dev环境的环境变量
// 在源码中会先进行判断mode是否存在。存在的话，先进行设置mode所对应的环境变量，然后在调用全局默认的配置文件
// 又因为dotenv 默认情况下，永远不会修改任何已经设置的环境变量。特别是，如果您的.env文件中有一个变量与您的环境中已经存在的变量发生冲突，那么该变量将被跳过。
// 所以设置了mode所对应的环境变量之后，在调用loadEnv()也不会更改相同设置的环境变量。
// 这也是为什么调用全局的环境变量不会替换mode所对应的环境变量的原因
```
