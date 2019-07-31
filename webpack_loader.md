### css-loader

当匹配到 css 文件时，就要用 css-loader 对 css 样式进行处理

css-loader 会处理 css 的资源，当它遇到一个 url 的时候，css-loader 会帮助我们处理 url 里面的东西，当出现 url，css-loader 就会知道，可能要加载一些东西，要引入一些东西，这时，css-loader 会帮助你去引入这个模块，之后 webpack 会帮助你处理这个模块

### style-loader

当有样式被打包到我们的入口文件时，style-loader 会把打包的样式插入到我们的 HTML 结构中

```
{
  test:/\.css$/,
  use:['style-loader','css-loader'],
}
```

use 中的 style-loader 和 css-loader 顺序不能变，因为 loader 的处理有一个优先级，从右到左、从下到上

很显然，只有当我们对 css 文件进行处理打包之后才将其插入到 HTML 结构中，所以要 css-loader 在右侧，style-loader 在左侧

### file-loader

专门处理图片

file-loader 主要做了两件事：1、把 url 里面内容转换成我们最终需要使用的那个路径。2、把图片转移到我们输出的目录，并且把图片更改为另外一种名字或者做一些其他的处理

```

import dog  from './common/imgs/dog.jpg'

ReactDOM.render(
    <div><img src={dog}/></div>,
document.getElementById('root')

 );
```

当我们在模块里面使用 import 来引入一个图片资源的时候，file-loader 也会把这个图片移动到你的输出目录，给它更改一个名字，然后返回一个最终要加载的图片的一个路径

在 css 的背景图片和 HTML 结构中插入的图片，两者的不同是：在 HTML 中插入图片需要自己手动引入一个图片资源，然后 webpack 会帮助你引入这个模块（资源），引入这个资源的时候，file-loader 会帮助你做相应的处理，而在 css 文件中，直接是正常的写法即可，不需要自己去手动 import 一个文件资源，因为 css-loader 在遇到 url 时，会帮助你处理 url 中的内容，它在处理时，就会帮你引入了这个图片资源，然后用 file-loader 来处理这个图片资源

### url-loader

引入图片，可以说它是 file-loader 的增强版

url-loader 会把我们的图片使用 base64 的形式编码成另外一种字符串，网页是可以识别这种编码的东西的，这样的好处是，它减少了图片的请求，你只要请求回了这个页面，图片也就过来了，可以减少网络的请求，但是如果图片过大，这个字符串就会变得特变大，让加载的文件变得特别大

所以如果图片很小，没必要让其重新请求图片，直接将其写进页面中，让浏览器去解析，当图片过大时，就不让他编码，看下面的实现过程

```
{
                test:/\.jpg|gif|png$/,
                use:[{
                    loader:'url-loader',
                    options:{
                        limit:10000//以bit为单位，当小于10000bit时，编码，大于10000bit时，不编码
                    }
                }]，
            }
```

总结：当使用 url-loader 去处理一些资源的时候，默认会把所有的资源都是用 base64 的形式进行编码，但是我们可以给它一个 limit 属性去约束他，当资源小于某个值的时候，才去编码，当不小于这个值时，它其实是会把这个资源交个 file-loader 去处理

### postcss-loader

加载 node_moduels 中的 CSS 之前还要使用 postcss-loader 再统一处理一遍，以确保所有进入生产环境的 CSS 都经过了相应的浏览器兼容性处理。

## 12、webpack js 常用 loader

### babel-loader

告诉 webpack 我想要对我的 js 代码进行兼容性编译
具体配置文件可以新建新建.babelrc, 通过配置一些参数配合 webpack 进行打包压缩

```
"babel-core": "^6.26.3",
"babel-eslint": "^8.2.6",
"babel-loader": "^7.1.5",
"babel-plugin-module-resolver": "^3.1.1",
"babel-plugin-syntax-dynamic-import": "^6.18.0",
"babel-plugin-transform-class-properties": "^6.24.1",
"babel-plugin-transform-object-rest-spread": "^6.23.0",
"babel-preset-env": "^1.7.0",
"babel-preset-react": "^6.24.1",
```

这里放出推荐配置

```
{
  "sourceMaps": true,
  "presets": [
    "env",
    "react"
  ],
  "plugins": [
    "syntax-dynamic-import",
    "transform-class-properties",
    "transform-object-rest-spread",
    ["module-resolver", {
      "root": ["./src"]
    }]
  ]
}
```

> presets

### babel-preset-env

之前配置话需要很多例如 babel-preset-es2015, babel-preset-es2016 或者 stage-0
但是现在只需要 babel-preset-env 就可以解决

### babel-preset-react

用于解析 jsx

> plugin

### syntax-dynamic-import

允许解析 import()

### transform-class-properties

用于解决 es6 的 class 的 defaultProps={} 不支持的问题

### transform-object-rest-spread

允许 Babel transform rest 属性用于对象的解构和对象的自变量.
