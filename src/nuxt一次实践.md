最近入职新公司。正好要开发一个官网。想到要官网要支持seo，遂选用nuxt进行开发。

运维使用k8s+docker进行部署

首先构建一个node + pm2的镜像

再根据node+pm2的镜像创建一个docker容器

正常的 (npm 和 yarn自己选择)
1、yarn install
2、yarn build
3、yarn start

在容器内执行以上三个命令的时候， 遇到的问题：

1、会发现找不到nuxt这个命令。
    解决：将所有的资源都直接copy到容器当中。 在容器中。进行一遍install.  这个是不是也可以在镜像中nuxt.然后根据node + nuxt + pm2的镜像来启动？
2、build和start都成功之后，外部却访问不了已启动的服务。
    原因是：在build和start的时候。都是默认localhost。导致的。（原来使用的nuxt默认启动的命令，未做更改）
    解决：发布的时候。需要指定HOST=0.0.0.0。不然当前是localhost。而导致外部不能访问线上配置的ip

配置域名：上述问题都解决之后，将ip配置指定的域名。运维也不知道咋赔。。听说是改了YARM文件。

1、发布的时候。在headerPc组件里.因为引入方式。@import 'HeaderPc.less';导致在服务器打包的时候，告知找不到文件。很奇怪。
    解决。这个文件的样式直接写在当前页面中，而不是用引入的方式。....沙雕。文件名错了。我擦（定义的名称是HeaderPC.less）

## fs的发布（直接丢到服务器）

### build 后压缩 `.nuxt` 文件夹、 `nuxt.config.js`、 `package.json`、`package-lock.json` 发给运维

因为运维是用npm安装依赖的，所以需要 `package-lock.json` 保持最新
推荐使用 nrm 插件切换淘宝镜像源

``` bash
# 安装nrm
$ npm i -g nrm
# 查看源
$ nrm ls
# 使用源
$ nrm use taobao
```
