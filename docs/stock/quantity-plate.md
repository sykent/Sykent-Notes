# 模板跟踪模型

## 通达信编程基础
### 快捷键
|快捷键|功能|空|快捷键|功能|
|:--:|:--:|:--:|:--:|:--:|
| Ctrl + F | 公式管理器 |-| Ctrl + R | 查看股所属行业分类 |
| Ctrl + T | 条件选股 |-| .902 | 扩展数据管理器 |
| PgUp/PgDn | 前后翻页 | - | .401 | 历史指标排序 |
| Esc | 返回 |

## 动量模型添加流程
* 下载盘后日线数据，第一次下载全部的，以后每天可以下载当天的。
* 下载专业财务数据。
* 自定义板块，添加板块数据。
* Ctrl + F 导入技术指标公司，简放的密码是111。
* .902 扩展数据管理器，27，28，29，30，31。<br/>
  步骤：先按文件夹的 “通达信动量模型设置（青山改良版）”，完成扩展数据 27-30；再按文档 “通达信动量模型设置（青山终极版）.doc”，完成扩展数据 31。
* .401 自定义板块指标数据排序。
* 流通股改成总股本。公式 DLJUDGE 中的流通股（239）改为总股本（238）。
* 可能动量会异常，那就自定义沪深A股即可。

数据交易日 5 月 7 号的效果图，如下：


![](https://sykent-blog-image.oss-cn-beijing.aliyuncs.com/stock/q-5月7号效果图.png)
## 板块动量曲线指标
**切换：** 板块动量曲线，图如下：

![](https://sykent-blog-image.oss-cn-beijing.aliyuncs.com/stock/q-板块动量曲线.png)

**说明：**
1. 这是三个不在一个数据区间的组合。所以大数做了缩倍。指标区右边的数据对动分是有效的。对其他两个无效（缩倍了），尽量不要看那个数。
2. 指标上边的数据显示是真实的数据。
3. 动序是反处理，符合大部分人的线性思维方式。这也是加了个扩展数据的原因。
4. RPS20 强度 87 线，是符合已经潜意识的 87 分数线了。
5. 动序 13 线，是去行业头部的 13% 比例，过线则进入 13% 的范围了。
6. 有些数据不要轻易改，不是瞎搞的。
7. 要想效果更好，三曲线各做一个指标，可自行处理，那样也太占空间了。

记住：就是看个图，感受板块的动量情况。不要钻牛角尖了。

## 简放表格分析
* 动量分值大于 7 风险提示，即将见顶。
* 动量分值大于 1 个数多，说明市场处于强势；大于 1 个数少，说明市场处于弱势。
* 动量分值大于 1 标红。
