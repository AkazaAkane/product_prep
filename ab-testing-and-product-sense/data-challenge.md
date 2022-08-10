---
description: https://www.1point3acres.com/bbs/thread-326201-1-1.html
---

# Data Challenge

#### 前期准备：

不管是sql,或者r, python，随便你选，但是一定要选你用的比较熟练的。因为你要短时间内完成数据分析+写报告，如果代码不熟练的话可能做不完。建议可以先准备一些模版，比如画图的，做模型的，做ab test的。我用的python，所以画图都是seaborn + matplotlib, 需要建模一律用random forest from h2O package。这里强烈推荐h2O random forest，自带auto bin的功能，解决了categorical level多的问题。不需要将 categorical variable 转化成numerical（对于python同学来说）, 不需要impute missing value。

#### 题目拿到手以后改咋办：

明确产品的目标： 一般都会给你描述一个产品，比如某社交网络公司想提高 rentention rate , 某电商公司想提高conversion rate。你下面的所有的分析一定要围绕这个目标来做。这个说起来容易，但是很多同学题目拿到手，都会脑补很多东西，想的太多了，反倒无从下手。建议就从跟产品目标最直观的开始分析

定义metrics：在清楚了产品的目标以后，哪些metrics可以用来衡量产品的成功与否呢。对于互联网产品，基本都是从user acquisition, retention, engagement. monetization 相关的这些目标来定义metrics的。多了解用户使用产品的漏斗模型 （AAARRR)。然后定义metrics的时候思考产品特点以及目标，往漏斗模型上面靠，每一层应该用什么metrics来衡量。可以看这篇科普的：http://startitup.co/guides/374/aarrr-startup-metrics

数据清理：也就是所谓的data cleaning。基本就是看看哪些变量的missing value太多了，或者某个变量只有一个level。这种情况下可以去掉那些没什么用的数据。另外如果你用h2O random forest建模，不用去impute missing value。

提取跟产品目标相关的变量：比如uber想提高driver retention rate，你拿到数据后，看一下每个变量都什么意思，想想哪些变量有可能跟目标相关。下面说一下我遇到的比较普遍的需要做一些data munipulation的相关变量

&#x20;

&#x20;   时间变量：可以提取day of week, month, time of the day这种变量。还有一些time difference， 比如user sign up date，first time use this product，这里面的时间差也就是用户登记后多久开始使用产品，这也会是一个很重要的变量。

&#x20;   需要求平均值，次数求和这种变量：比如一周内使用了多少次产品，平均每次花了多少钱

&#x20;   去掉跟结果直接相关的变量：比如某个变量跟结果是显而易见的相关，虽然加入这个变量你的模型预测准确度达到99.9999%， 但是对于你后面做的产品推荐没有任何意义。比如某电商想看看用户的哪些行为能够促使最后花钱买产品，有个变量是是否到了check out页面。很显然用户到了check out 页面，购买的意向就已经很高了。在建模的时候要去掉这个变量，因为不用分析就知道这个变量重要。

&#x20;

如何鉴别重要的变量：一般的问题都是让你鉴别哪些变量对结果影响最大。选3-4个重要变量即可，千万不要把所有的都分析了，因为你没有时间！下面说两种我常用的方法

&#x20;

&#x20;   看分布：比如你觉得time difference是个很重要的变量，可以画个box plot，或者histogram，分别对retain and churn的人做图

&#x20;   直接用模型：根据模型结果看feature importance。我只用random forest。因为第一我建模的目的只是为了看哪个变量重要，并不需要很精确的预测；第二用h2O的random forest基本不用调试，结果就很不错了；第三我觉得random forest在鉴别feature importance比别的模型要好，因为它每次是取所有变量的一个子集来建立决策树，所以每个决策树选的变量都不太一样。最后平均下来看哪个feature最重要。感觉这种算法更可靠一些。不过哪种模型不重要，关键你把重要的变量选出来就好。这里提示一点：千万不要花时间去把模型调的很精确，只要模型结果可以接受就行。因为你是在做分析，你的重点是在做后面的产品改进推荐

&#x20;

产品改进推荐：也是最最最重要的一点！很多同学做模型啊，分析啊做的天花乱坠，然后都挂在这步了。一定要记住一点，你的模型是为了产品推荐用的，不是为了production用的。比如你发现用户登记以后越快使用你的产品，他们的rentention越高，那么就要想办法如何让用户尽快使用你的产品。你不能只说让用户尽快使用产品，要给出更具体的建议。比如给登记的用户发promotion，第一次购买可以便宜一些。有的职位偏重AB test，会问你接下来如何设计实验来测量你的推荐的有效性

实验设计：必看资料是udacity上面的AB test by Google  一般做题目常用的无非就是test mean difference or proportional difference ( t and z test), 上面都讲的很清楚应该如何做，如何选sample size。下面简要说一下如何分析结果

&#x20;

&#x20;   影响有多大：也就是what is the opportunity sizing. 这一点很重要，如果你的产品推荐只会对很少一部分人有影响，比如小于5%，那么你这个推荐是没有用的。但是有一个特例就是如果你那5%的人可以带来好几个million的收入增加，那么还是值得做的。

&#x20;   分析比较结果：比较爱问的问题有

&#x20;

&#x20;       Is this amount of lift enough? 比如做实验后发现有2% lift，这个结果好不好？这种题目一般就要看2% lift带来的实际影响，比如2%带来了几个million的收入的增加，那么就是好的。

&#x20;       Metric A going up, Metric B going down, should we still launch this product? 一般看哪个是最重要的metric，另外就是有些metric需要时间长一些才能看出来。比如某个社交网络的用户参与度增加了但是用户增长变慢了，假设这个产品改善后是希望增加用户的参与度。这种情况就要考虑network effect，随着用户的参与度的增加，用户的connection也会受到影响，久而久之他们也会变成日活或者月活的用户。

分析做完了，写报告应该注意啥：

思路清晰，言简意赅：看似是废话，但是很多同学，包括我以前，都恨不得做个特别复杂完美的图跟表格，然后展示给面试官我的技术有多牛掰。其实他们更看重的是你的分析是不是通俗易懂，非technical的人能不能一看你的图或者分析就知道怎么回事了。

图文并茂：这里强烈推荐大家都鄙视的excel作图功能，个人觉得比seaborn, ggplot, matplotlib都好用多了。也许是因为我代码能力不强，改个图得debug半天，还经常弄不出自己想要的效果，但是用excel简直是神器，轻松做出非常专业的图来，改起来也很方便。我一般简单的图，比如boxplot, heatmap，用seaborn这种直接出，但是要做一些复杂的cohort analysis，就上excel了。

不要写的太长：很多同学把data challenge当成论文来写，弄个几十页的报告，把能分析的都分析了一遍，结果还挂了。因为人家面试官根本没有时间看你的论文报告。确保他们花10-15分钟时间能把你的分析跟结论看懂。

我现在能想到的就这么多。最后总结一下主要步骤： 明确产品目标， 定义相关metrics，建模去预测关键指标，模型结果对产品改进有啥建议。希望这篇总结能对正在战斗或者打算战斗的战友们有点帮助。
