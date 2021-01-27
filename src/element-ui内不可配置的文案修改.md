### element-ui内不可配置的文案的修改问题

    起因：一个后台管理系统的分页组件（使用vue2+element）。ui小姐姐收到甲方爸爸的修改意见，说要改一个文案。听了之后窃喜（这么简单，那不是秒秒钟的事），ui和我同步了修改点之后。
![](./../images/jump.png)

    隐隐约约感觉有点不对劲。这个文案应该是组件不可配置的。
    回element-ui官网查pagination组件，果然，这是不可配置的。
    当时内心心情如下图所示

![雷佳音。我尼玛](./../images/nima.gif)

仔细一想，其实也很简单。

```
1、在pagination基础上封装的table+分页组件内，直接修改dom，很容易就解决了

2、虽然问题是解决了。但是，不可能以后每改一个文案，就得去操作一次，那不是失去使用vue的初衷了吗，遂去看element-ui的pagination组件源码。
```

```
// 文件夹目录： element-ui\packages\pagination\src\pagination.js中

import Locale from 'element-ui/src/mixins/locale';
render(h) {
    return (
        <span class="el-pagination__jump">
            // 这个代表文案'前往'
            { this.t('el.pagination.goto') }
            ...
            // 这个代表文案'页'
            { this.t('el.pagination.pageClassifier') }
        </span>
    );
}
// 这个this.t就是在mixins中定义的。引用了import { t } from 'element-ui/src/locale';

// 在element-ui/src/local这个文件夹下。我们发现了
import defaultLang from 'element-ui/src/locale/lang/zh-CN';

const lang = defaultLang

Vue.locale(
    Vue.config.lang, // 语言
    deepmerge(lang, Vue.locale(Vue.config.lang) || {}, { clone: true }) // 对象
);

在这个文件内找到了对应的el.pagination.goto值
```

```
2、遂在项目当中main.js中对应代码
Vue.locale(
    locale: 'zh',
    messages: {
        el: {
            pagination: {
                goto: '跳转123123'
            }
        }
    }, 
);
执行。报错：locale不是一个方法

需要用到语言包 https://kazupon.github.io/vue-i18n/guide/component.html。因为是在element-ui第三方组件内的。所以就参考了'基于组件的本地化'部分，发现，element-ui就是这么实现的。

```

```
// 最终我们在main.js做以下修改
import zhLocale from 'element-ui/lib/locale/lang/zh-CN'
import ElementLocale from 'element-ui/lib/locale'
Vue.use(VueI18n)
const zhLocaleObj = {...zhLocale}
zhLocaleObj.el.pagination.goto = '跳转123123'
const i18n = new VueI18n({
    locale: 'zh', // set locale
    messages: {
        zh: zhLocaleObj
      }, // set locale messages
  })
  
ElementLocale.i18n((key, value) => i18n.t(key, value))
即可。参考https://element.eleme.io/#/zh-CN/component/i18n
```

[可参考这个demo](https://github.com/benfangdesaozhu/study/tree/master/webpack/test)