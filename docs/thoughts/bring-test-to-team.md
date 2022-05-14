# 如何把测试带给团队

## 前言

哈喽，大家好，我是海怪。

之前发了很多关于前端单测的文章，评论区里经常看到这样的留言："最近也在搞前端单测..."。看来大家在给项目引入测试过程并不是很顺利。当然我自己在引入测试的时候也有很多困难，
特别面对那种庞大且完全没有测试的项目时，一时之间不知该从何入手。

[Kent C. Dodds](https://kentcdodds.com/ "Kent C. Dodds") 这个老哥正好写了一篇关于如何在团队中引入测试的文章 [《How to add testing to an existing project》](https://kentcdodds.com/blog/how-to-add-testing-to-an-existing-project "《How to add testing to an existing project》")，
今天就把这篇文章分享给大家。

> 翻译中会尽量用更地道的语言，这也意味着会给原文加一层 Buf，想看原文的可点击 [这里](https://kentcdodds.com/blog/how-to-add-testing-to-an-existing-project "原文")。

---

## 正片开始

相信大家都有这样的经历：在做一个 "绝对不可能像这样上线" 的原型网页。或者你可能连做原型的时间都没有，所以你肯定不会写测试的。或者你加入了一个以前没有写过测试的团队（也有可能只是你自己写测试）。
然而，有一天出了线上事故，在此之后每次要点"发布"按钮时总是胆颤心惊。你还得继续写新需求和修 Bug，最终你受不了了，决定要给项目上测试，想要更高的代码信心。

不过，你该从何开始呢？

你的项目可能有上千、上万行代码，这个任务太大了以至于你都不敢想。但你总得从某个地方开始。好，现在让我们来放大整个 [测试模型](https://kentcdodds.com/blog/static-vs-unit-vs-integration-vs-e2e-tests "测试模型")，
看看上面哪些策略可以应用到你的项目。

下面就是整个测试模型，详情可以看这篇 [文章](https://kentcdodds.com/blog/static-vs-unit-vs-integration-vs-e2e-tests)。

![](https://files.mdnice.com/user/24913/2c0fbf21-3d89-4f05-810d-786ce16eb31d.jpg)


刚开始的时候，你可以优先关注那些能提供最佳回报的事情上。下面就是我们要做的：

## 第一步：设置静态检查工具

静态检查工具包括：
* Linter: [ESLint](https://eslint.org/)
* 格式化: [Prettier](https://prettier.io/)
* 类型检查: [TypeScript](https://www.typescriptlang.org/) / [Flow](https://flow.org/)

到目前为止，设置 ESLint 是您可以应用于代码库的最低 “成本”，并且可以帮你避免一堆的的错误。由于其可配置性，你也可以逐步应用到项目上。
引入之后可以先把报 Linter 错误的规则改成 warning 提醒，然后在以后的时间里再慢慢修改好这些规则报错。

Prettier 也很容易配置，不过你可能要花点时间说服那些不喜欢用代码格式化工具的人。你可以看 ["Why Prettier"](https://prettier.io/docs/en/why-prettier.html) 来了解它为什么这么有用。
我会把它归类为静态测试检查工具，因为如果你写了语法错误，那 prettier 会格式化有问题的代码。

TypeScript 和 Flow 可能会有点麻烦。这两也可以慢慢引进项目，但是配置它们以及让它们在开发过程中能正常工作会花不少时间和精力。就算这些工具也只是让 JavaScript 拥有自己的类型，但是也有一定的学习成本。
不过，我可以告诉你这些静态类型检查工具是值得学习的。我能给你的一个建议就是：不要在刚开始就要求项目有完美的类型定义。不要害怕用 `any`（或 `known`）！
它们可能在做类型检查时不是那么有用，但我见过很多人放弃 TypeScript 的一个原因是，他们花了很多时间去想怎么才能写好类型。
不完美就不完美吧，你可以在学到更多之后再回过头来更新迭代。

想了解更多关于静态测试工具的价值，详见：[《Eliminate an entire category of bugs with a few simple tools》](https://kentcdodds.com/blog/eliminate-an-entire-category-of-bugs-with-a-few-simple-tools "《Eliminate an entire category of bugs with a few simple tools》")。

## 第二步：写一个简单的 E2E 测试

现在我们的目标就是从这个测试模型的底部走到顶部，这需要相当大的努力，同时回报也是巨大的。有很多种方法可以实现，但可能最简单的方式就是写一个生产环境的 E2E 测试（或者一个类似生产环境的环境），
然后走通最普遍/最重要的用户流程。这个测试可能很长，但没事（反正 E2E 写太短也是 [常犯的错误](https://kentcdodds.com/blog/common-testing-mistakes)）。
不过，可以想像一下，如果你可以招到一个手工 QA，在每次部署完都手工给你跑一次项目里重要的用户流程，那多是一件美事呀。这是多么有价值呀。

这个测试不一定简单。这要取决到你的项目有多复杂，有可能要一天或者几天才能在 CI 上跑上这个测试。如果你想用定时任务每一小时跑一下测试，也是可以的。就算你不能让它跑在 CI 上也没问题。
可以慢慢迭代到最佳版本。不要让完美阻碍你的脚步。

## 第三步：写一个单测

我们再来看下一个测试类型。单测是最容易写的测试了。直接选项目里最简单的一个 [纯函数](https://en.wikipedia.org/wiki/Pure_function) ，安装和配置对应的测试工具，然后把它给测了。
一旦有一个东西被测了，那么写其它的测试就非常简单了。很多人不写测试，就是因为配置工具这块非常麻烦。所以你的工作是准备好工具，让别人更容易地写测试。

## 第四步：写更多测试

到这一步，你应该已经准备好所有的工具和配置，可以写更多的测试了。现在可以把重心放在写更多的集成测试。
每一个测试你都会遇到新的挑战，你可能需要写一些模块的 Mock。不过，当你做的越多，未来的测试就越容易。

你可能会发现有趣的是，直到这一步我才建议你开始关注集成测试。虽然 [它们通常会为你带来最大的收益](https://kentcdodds.com/blog/write-tests) ，但是在测试工具还没准备、配置好之前，集成测试是很难写的。
而且，和集成测试一样有价值，第一次全面完整的 E2E 测试会更有价值。

## 第五步：教你的团队如何写测试

现在所有工具都已就位，你的团队就可以开始给他们创建和维护的代码写测试了。

（译注：后面都是作者对他的 [TestingJavaScript.com](https://testingjavascript.com/) 的宣传，我就不翻译了。关于这个课如何，我扫了一眼再结合这个人的文章来看，应该是不错的，不过价格属实有点贵。之后我应该也会买来看看，到时再做一波分享。）

## 总结

我希望这对你不断改进项目中的测试有所帮助。通过让人信心满满的测试策略，你晚上会睡得更好。😴 晚安。

---

**好了，这篇外文就给大家带到这里了。这篇文章主要聊了一下如何把测试引入项目里，如果大家也在做同样的工作，可以参考一下这 5 个步骤。其实我一直觉得写代码就像输出，做测试就像是打辅助，对于一个辅助来说不是越多输出奶越好，而是要针对目前的情况来决定要给谁奶，要复活谁。
其实是更需要了解整个项目的架构和功能的。而且引入测试的一大难题往往是配置，很多人以为写测试就装一个 Jest 就开干，但我们项目往往会有很多 Tricky 的东西，这就需要一些配置才能支撑测试环境，所以这篇文章也说了配置好了，后面写测试代码是很轻松的。**

**如果你喜欢我的分享，可以来一波一键三连，点赞、在看就是我最大的动力，比心 ❤️**
