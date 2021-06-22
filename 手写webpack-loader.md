#### 手写webpack-loader步骤

题外话：因为前段时间做了h5的一个系统，为了适配各种屏幕，就用到了把px转化为rem的问题，我们使用的库是先把用[lib-flexible](https://github.com/amfe/lib-flexible)设置根元素大小，所以在开发中就不用我们自己去计算改写多少rem。只需要根据UI设计师的图纸，写出觉得的像素大小即可。那么我又遇到另外一个问题，iphone手机的分辨率和我们平时开发的不太一样，一般UI设计图纸都是按照750px宽的比例来的，但是呢我们的开发标准一般使用iphone6来定，所以我们需要除以2，350px才是实际开发宽度，才能使我们的分辨率达到最清晰。

回到正轨：最近网上看了视频，跟着学了下webpack的loader造轮子过程，分享给大家。一般我们手写loader是因为市面上loader不能满足我们平时开发需求，所以不得不动手写一个自己的loader。

步骤很简单，只需要在本地创建文件，引入相应的工具包，最后加入webpack的loader中即可。这里我仿造了把px转化为rem的一个loader[px2rem-loader](https://www.npmjs.com/package/px2rem-loader)，代码如下，

```js
    module: {
        rules: [
           {
            test: /\.css$/,
            use: [
                'style-loader',
                'css-loader',
                {
                    loader: path.resolve('loader/px2rem-loader.make.handle'), // 我们自己的loader，引入方法很多种，这里不一一列举，使用的绝对路径
                    options: {
                     remUni: 75, // 转化基数
                     remPrecision: 8 // 转化后的保留小数位
                    }
                }
            ]
           },
        ],
    },
```

首选我们在配置文件中引入以后，就可以动手写代码。代码和源码中有些差距，因为源码更全面，更详细。

这里我用到了两个库

1. 把css转化为AST的工具库[css](https://www.npmjs.com/package/css)
2. 获取loader的options参数[loader-utils](https://github.com/webpack/loader-utils)

引入库以后，开始写代码



```js
// 我们主要加载的loader文件px2rem-loader.make.handle.js
const loaderUtils = require('loader-utils')
const Px2rem = require('./px2rem.make.handle')

function loader(source){
    const config = loaderUtils.getOptions(this) // 获取options配置
    console.log(source)
    const px2remIns = new Px2rem(config)
    return px2remIns.generateRem(source)//css源代码
}

module.exports = loader
```

主要核心了逻辑使用css把源代码转化为css 的AST对象，通过正则匹配数字加px获取到我们想要替换的数字。最后把ast转化为源代码即可

ast对象结构示例：

```js
{
  "type": "stylesheet",
  "stylesheet": {
    "rules": [
      {
        "type": "rule",
        "selectors": [
          "body"
        ],
        "declarations": [
          {
            "type": "declaration",
            "property": "background",
            "value": "#eee",
            "position": {
              "start": {
                "line": 2,
                "column": 3
              },
              "end": {
                "line": 2,
                "column": 19
              }
            }
          },
          {
            "type": "declaration",
            "property": "color",
            "value": "#888",
            "position": {
              "start": {
                "line": 3,
                "column": 3
              },
              "end": {
                "line": 3,
                "column": 14
              }
            }
          }
        ],
        "position": {
          "start": {
            "line": 1,
            "column": 1
          },
          "end": {
            "line": 4,
            "column": 2
          }
        }
      }
    ]
  }
}
```



```js
const css = require('css');

const pxRegExp = /\b(\d+(\.\d+)?)px\b/;

class Px2rem {
    constructor(config) {
        this.config = config
    }

    generateRem(source) {
        // const { remUni, remPrecision } = this.config;
        const cssAst = css.parse(source)

        console.log(cssAst)

        const processRule = (rules) => {
            if (!rules) return
            for (let i = 0; i < rules.length; i++) {
                const rule = rules[i];
                if (rule.type === 'media') {
                    processRule(rule.rules)
                    continue;
                } else if (rule.type === 'keyframes') {
                    processRule(rule.keyframes)
                    continue;
                } else if (rule.type !== 'rule' && rule.type !== 'keyframe') {
                    continue;
                }
                const declarations = rule.declarations;
                for (let j = 0; j < declarations.length; j++) {
                    const declaration = declarations[j];
                    // 蜜汁操作
                    if (declaration.type === 'declaration' && pxRegExp.test(declaration.value)) {
                    console.log(declaration.value)
                    declaration.value = this._getValue('rem', declaration.value)
                    }
                    
                }
            }
            console.log(cssAst)
        }
        processRule(cssAst.stylesheet.rules)
        return css.stringify(cssAst)
    }

    _getValue(type, value) {
        const {
            remUni,
            remPrecision
        } = this.config;
        return value.replace(pxRegExp, (_, $1) => {
            return type === 'px' ? (parseFloat($1) / remUni).toFixed(remPrecision) + type : parseFloat($1) / remUni + type
        })
    }


}


module.exports = Px2rem
```



通过此实例，学会了如何去造一个简单的loader。

