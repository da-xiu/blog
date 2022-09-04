<!--
 * @Descripttion: 
 * @LastEditTime: 2021-08-18 20:37:43
-->
#### javascript设计模式学习

最近在看《javascript设计模式》这本书，此书中使用的代码实例相还是以前的es5，不过很经典，很有学习价值。其次设计模式的解读环环相扣，很有意思。通过学习对设计模式有了更进一步的了解，摸鱼时间记录一下。

1. 单体模式

   概念: 仅提供一个方法调用，返回一个对象，而且只调用一次

   模式的优点：防止变量污染全局，简单易用

2. 工厂模式

   工厂模式顾名思义，把所有的零件拿过来组装成一个完整的工具，这就是工厂模式的作用。

3. 策略模式

当我们在表单校验或者要使用一系列**if**判断时，我们可以使用建议策略模式实现。 比如我们原始代码校验简单表单时

```js

const submit = () => {
   if (phone_number.length > 12) {
   return Promise.reject('号码有误！')
}
if (/@|#/.test(name)) {
  return Promise.reject('姓名中不能有特殊字符！')
}
if (email...) {
 return Promise.reject('邮箱格式错误')
}
}

```

诸如此类面条代码，我们可以做简单改造

```js

const strategies = {
   validatorPhone: (phone_number, massage) => {
      if (phone_number.length > 12) {
   return massage
}
   },
   validatorName: (name, massage) => {
      if (name...) {
   return massage
}
   },
   validatorEmail: (email, massage) => {
      if (email...) {
   return massage
}
   },
   
}

class Validator() {
   validatorFn = []
   add(fn, value, message) {
      validatorFn.push(() => strategies[fn].call(value, message))
   },
   start() {
      for (let item of validatorFn) {
         const message = item()
if (message) return message
      }
   }
}

const runValidator = (value) => {
const validator = new Validator()
// 添加校验
validator.add('validatorPhone', value.phone, '号码有误！')
...
return validator.run()
}

const submit = (value) => {
const message = runValidator(value)
if (message) {
   alert(message)
}
}

```
从上面重构可以看出，使用策略模式重构更符合代码设计的开闭原则，写出来代码易于理解，也很好扩展，但是缺点就是增加一些代码量，这对于有代码量要求的，可以取舍使用。
