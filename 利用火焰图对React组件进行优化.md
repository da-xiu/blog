## 利用火焰图优化

1. 首先安装[React devTools](https://github.com/facebook/react/tree/main/packages/react-devtools) 
2. 在浏览器控制台看到Profiler
3. [如何调试](https://reactnative.dev/docs/debugging#chrome-developer-tools)
4. 点击左上角圆圈，开始记录。
5. 得到的svg图，可以根据组件颜色深浅来判断，颜色暖色，代表组件耗时过多，我们可以进行优化。