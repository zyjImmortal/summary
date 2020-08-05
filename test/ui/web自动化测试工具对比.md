WEB测试最常用的工具可能就是[selenium](https://pypi.org/project/selenium/)了，但是我在之前的使用中也遇到了一些不方便的情况：

- WebDriver和浏览器版本需要对应；
- 虽然是跨浏览器的脚本，但有时还是要对不同的浏览器做一些适配；
- selenium通过WebDriver驱动浏览器，执行的不够快；

最好是能够跳过WebDriver，直接驱动浏览器。目前这种测试框架有[cypress](https://github.com/cypress-io/cypress)、[testcafe](https://github.com/DevExpress/testcafe)、[puppeteer](https://github.com/puppeteer/puppeteer)等，不过它们都是Node.js的框架，而javascript足以让大多数测试人员望而却步。

我之所以选择Puppeteer，而不是Selenium的原因有以下几点：

- Selenium致力于跨浏览器的解决方案，而现实中Chrome的市场份额已经很高了。我之前用Selenium写的脚本，很少要求在其它的浏览器上执行，反而增加了维护的成本；
- Selenium配置繁琐，而Puppeteer几乎是免配置的；
- Puppeteer方便进行异步操作，比如同时执行两个浏览器同时下发配置；之所以有这种场景，是我的工作性质决定的，我的测试对象是通信设备，每台设备都会提供WEB服务，而且通常的多台设备组成一个场景进行测试，那么同时配置多台设备肯定要方便的多；

