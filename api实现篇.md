### 实现js原生api

1. 实现new
```js
    function mockNew(targetObj, ...params) {
        const obj = {}
        // 原型继承
        obj.__proto__ = targetObj.prototype;
        let r = targetObj.apply(obj, params);
        return r instanceof Object ? r : obj;
    }
```
2. bind实现

```js
 Function.prototype.mockBind = function(context) {
        const that = this
        // 参数
        const bindArgs = Array.prototype.slice.call(arguments,1)
        function Fn() {}// 重新定义的目的避免原型链引用
        function fBound() {
            // 当使用new bind
            return that.apply(this instanceof fBound ? this : context, bindArgs);
        }
        Fn.prototype = this.prototype
        fBound.prototype = new Fn();
        //为什么不这样直接赋值？原因是当我们修改fBound的原型时，对this的原型不产生影响
        // fBound.prototype = this.prototype;
        return fBound;
    }
```

3. 实现call

```js
    Function.prototype.mockCall = function (context, ...args) {

        context = context ? Object(context) : window
        // 改变this指向，让this指向context
        context.fn = this
        // 如果不用es6扩展符，我们可以这么做
        // const args = []
        // for (let i = 0; i < arguments.length; i++) {
        //     args.push('arguments[' + i + ']')
        // }
        let r = context.fn(...args)
        delete context.fn
        return r
    }

    function testCall() {
        console.log(this, arguments)
    }

    testCall.mockCall(obj, '2', '3')
```

4. 实现apply，实现原理跟call相似，唯一不同点只是传参形式不同

```js

    Function.prototype.mockApply = function (context, args) {

        context = context ? Object(context) : window
        // 改变this指向，让this指向context
        context.fn = this

        // if (!args) { 
        //     return context.fn()  
        // }
        // 三个参数一起传，但是fn接收的时候是一个个单独的参数
        let r = context.fn(...args)
        delete context.fn
        return r
    }

    function testApply() {
        console.log(this, arguments)
    }

    testApply.mockApply(obj, [1, 2, 3])

```