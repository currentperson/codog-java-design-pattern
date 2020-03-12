\# &lt;重构&gt; 读书笔记

&lt;a name="c4ef9fc6"&gt;&lt;/a&gt;

\#\# 一. 代码的坏味道\(原书第三章\)

&lt;a name="d4441914"&gt;&lt;/a&gt;

\#\#\# 1. 重复代码

\[http://linka.58cloud.alipay.net/index.html\#/report/statistics/5c500e6a796d20e891dfd6d1?\_k=dhehy1\]\(http://linka.58cloud.alipay.net/index.html\#/report/statistics/5c500e6a796d20e891dfd6d1?\_k=dhehy1\)&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548815746564-50b93e9d-945f-4d91-96e4-3fa21ca21595.png\#align=left&display=inline&height=272&name=image.png&originHeight=544&originWidth=2160&size=130813&width=1080\)&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548815945876-b6143c7c-3dc6-40f3-b178-c3caaf1296df.png\#align=left&display=inline&height=272&name=image.png&originHeight=544&originWidth=2270&size=115568&width=1135\)&lt;br /&gt;分析:&lt;br /&gt;1.1 在渠道层主要是一些 convert 等一些私有方法, 使用 idea 工具重构成 private 方法, 没有抽取到 Util 工具类或者更通用的 manager 中造成重复&lt;br /&gt;1.2 业务迭代 DRM 开关关闭和打开的时候的业务代码重复程度高&lt;br /&gt;1.3 二个相似类内部代码相似, 没有抽出公共基类, 接口等&lt;br /&gt;1.4 大量入参拼装代码相似, set 十几行, 没有抽成公共的 requestBuilder

&lt;a name="ff38d1ae"&gt;&lt;/a&gt;

\#\#\# 2. 过长函数

过长的函数不只是指代码行数长的函数, 还指函数内部包含代码语义与实现手法之间的差异大的代码的函数,也就是 "做什么" 和 "怎么做" 之间的语义差距, 寻找重构机会:&lt;br /&gt;2.1 函数内部代码长度长&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.discount.QueryVisibleDiscountCardsComponentImpl\#queryPersonalDiscountCardVOs&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548817025265-ea7f7215-d30a-4888-9af5-23e53441ee77.png\#align=left&display=inline&height=459&name=image.png&originHeight=917&originWidth=1063&size=299176&width=532\)&lt;br /&gt;2.2 需要注释单独说明的代码段&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.loanpay.impl.LoanPayComponentImpl\#initLoanPayForm&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548817375145-620214a1-a7f3-45ec-905a-1bae7de81df6.png\#align=left&display=inline”&height=454&name=image.png&originHeight=908&originWidth=890&size=255832&width=445\)&lt;br /&gt;2.3 分支和循环代码&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.loanscheme.LoanSchemeApplyComponentImpl\#queryLoanCanIncrease&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548817641312-2a0fdcd3-fabc-408b-aa9c-a04899bab3ab.png\#align=left&display=inline&height=437&name=image.png&originHeight=873&originWidth=907&size=234423&width=454\)



&lt;a name="e2d9f41a"&gt;&lt;/a&gt;

\#\#\# 3. 过大的类

com.mybank.bkloanportal.biz.shared.bkmportal.component.production.LoanVisibleProductComponent

类里面包含产品查询, 产品排序, 产品视图拼装, 产品合并, KYC 担保, 产品新老平台切换等功能, 一个类包含 17 个接口, 实现类 1300 行, 属于过大的类

&lt;a name="64a5b78e"&gt;&lt;/a&gt;

\#\#\# 4. 过长/过短参数列

过短参数列:&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.promotioncredit.PromoteCreditCallbackTaskComponentImpl\#applySubmit&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548818419602-25d1ec1b-15ec-4c7b-85b3-29e8c9869e1a.png\#align=left&display=inline&height=221&name=image.png&originHeight=442&originWidth=839&size=105586&width=420\)

!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548818458235-38902a02-21ab-49aa-b5bc-5f55bbb059a7.png\#align=left&display=inline&height=197&name=image.png&originHeight=394&originWidth=548&size=59697&width=274\)&lt;br /&gt;参数中的聚合字段过多, 调用方不清楚应该必须填入那些字段, 导致函数无法复用



过长参数列:&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.manager.LoanDiscQueryComponentImpl\#initDiscountConsultRequest&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548818805826-fd638a82-25a0-4c21-a9b9-88431f5ad093.png\#align=left&display=inline&height=75&name=image.png&originHeight=149&originWidth=1035&size=35024&width=518\)&lt;br /&gt;参数列过长, 可以编写中间参数用来传递, 比如 ipId,IpRoleId和 alipayId 可以组成同一个入参 userIds, pdCode,loanTerm,repayMode 等变成 productionInfo 传递, 可以有效减少参数长度 



&lt;a name="8cd4f43c"&gt;&lt;/a&gt;

\#\#\# 5. 发散式变化

针对某一外界变化的所有相应修改, 都只应该发生在单一类中, 而这个新类内的所有内容都应该反应此变化&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.discount.QueryVisibleDiscountCardsComponentImpl&lt;br /&gt;查询用户下所有优惠有二个维度的代码变化, 一个是用户可见产品的查询, 一个是查询单个产品优惠券的集合



可见产品包括过滤掉政策和实时不准入, 过滤 1001 产品等, 如果需要产品或者优惠券逻辑的改动, 这个类都需要改动, 所以应该把查询产品和代码再封装到产品组件中



&lt;a name="ec3a4701"&gt;&lt;/a&gt;

\#\#\# 6. 散弹式修改

与 5 正好相反, 一个变化需要修改多个类, 可以把相关的类聚合在一起&lt;br /&gt;比如目前提出的优化贷款记录需求, 需要把原来的2 年限制放开到 5 年, 贷款记录的用处有展示类表, 计算余额, 计算逾期额度等, 要修改UserInfoComponment 和 LoanRecordComponment 几个类进行支持, 可以考虑将贷款记录的能力合并起来

&lt;a name="c3268a5c"&gt;&lt;/a&gt;

\#\#\# 7. 依恋情节

某一个类 A 中的某个函数为了计算一个值, 调用了另外一个类 B 中的大量方法和属性, 可以考虑将方法放入 B 中&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.user.UserInfoComponentImpl\#querySelectedAccount&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548820843211-a9aafb8a-83ee-4521-b29e-99c4bae796c2.png\#align=left&display=inline&height=366&name=image.png&originHeight=732&originWidth=1167&size=203257&width=584\)&lt;br /&gt;在 userInfoComponment 中, 但是里面全部调用的 arrangementQueryManager 中的方法, 可以考虑将此方法移动到 arrangementQueryManager 中



&lt;a name="6cdff593"&gt;&lt;/a&gt;

\#\#\# 8. 数据泥团

指的是多个数据相互关联, 又缺一不可&lt;br /&gt;在渠道很多 ipId, ipRoleId, alipayId, siteUserId 等字段经常需要传入同一个函数做入参, 这些字段相互关联, 缺一个都不行, 所以又必要在用到的时候作为一个整体入参或者返回值传递, 比如定义一个 userInfoDTO 用来指定这些信息



&lt;a name="9b5fec74"&gt;&lt;/a&gt;

\#\#\# 9. 基本类型偏执

不愿意定义小对象, 使用基本类型组装, 与数据泥团相似, 好的例子, 比如 money 类, 结合了数值和币种和相关的操作, 十分有用, 本书作者建议建立 Range 类, 结合渠道比如查还款计划和贷款记录的时候, 都是需要指定 startDate 和 endDate 而二个字段, 目前都是区分开的字段, 个人发挥觉得比如 pageNo 和 pageSize 而二个字段在分页查询的时候也可以当成小对象传给函数



!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548828073102-a3ce2ed4-4503-4921-bef8-3ba083dd80f0.png\#align=left&display=inline&height=260&name=image.png&originHeight=520&originWidth=636&size=75360&width=318\)



&lt;a name="9265b600"&gt;&lt;/a&gt;

\#\#\# 10. switch 语句

switch 语句在简单情况下十分有用, 但是如果分支复杂, 就会引起代码难以理解, 重复无用等情况, 可以采取策略模式等消除 switch 语句&lt;br /&gt;com.mybank.bkloanportal.biz.mportal.service.impl.common.DistrictExternalQueryProcessor\#filter&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548828345738-a8a29484-1bdb-4487-9e80-1354f675d40d.png\#align=left&display=inline&height=303&name=image.png&originHeight=605&originWidth=892&size=148264&width=446\)&lt;br /&gt;上面的代码就有些代码重复的嫌疑&lt;br /&gt;建议改进如下:&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548829071807-140562b2-0eac-4a92-bb48-d30f0cdce231.png\#align=left&display=inline&height=135&name=image.png&originHeight=270&originWidth=1194&size=31828&width=597\)

&lt;a name="b410487e"&gt;&lt;/a&gt;

\#\#\# 11. 过度耦合的消息链

A 对象调用 B, B 再调用 C, C 再调用 D etc 才能获取到真正的值, 实际表现比如 a.getb.getc.getd 等, 这种坏处是调用方还要经常判空, 调用方要明确对象之间的关系, 对象之间的关系变化时, 调用方也不得不随着变化调用方式&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548830097147-45bea16b-4407-4948-990e-4e6ff195bfff.png\#align=left&display=inline&height=196&name=image.png&originHeight=391&originWidth=1079&size=96671&width=540\)&lt;br /&gt;在渠道这面一个很明显的例子是获取上下文入参中的 alipayId, 调用方要取到  alipayId 必须要知道 portalRequest 中取 userRequest 再取 alipayId, 如果关系变化了, 所有的代码将不得不改, 风险很高.&lt;br /&gt;建议改动为调用方改为 portalRequest.getAlipayId\(\), 内部实现如下



\`\`\`java

PortalRequest{

	public String getAlipayId\(\) {

   	return this.userRequest.getAlipayId\(\);

	}

}

\`\`\`



&lt;a name="0a501d7a"&gt;&lt;/a&gt;

\#\#\# 12. 不完美的类库

一些工具类可能不满足需求, 可以通过继承重写或者外面包一层来解决&lt;br /&gt;比如原始的 uuid 生成不满足渠道需求, 定制化重新提供&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548831391665-7147a6ae-6100-4f4e-928c-2d03924802c8.png\#align=left&display=inline&height=276&name=image.png&originHeight=551&originWidth=823&size=120922&width=412\)



&lt;a name="3c1fc50c"&gt;&lt;/a&gt;

\#\# 二. 工具准备\(原书第四章\)

如果想使重构变得代价更低, 效果更好, 总结一下要有自动化测试工具, 和性能分析工具是必不可少的

&lt;a name="688b8f10"&gt;&lt;/a&gt;

\#\#\# 性能分析工具介绍: VisualVM

bkloanportal sit 环境情况, 可以用来分析函数耗时和函数 CPU 耗时, 来找到 IO 耗时高的函数和 CPU 耗时高的函数, 致力于提高系统性能的重构可以往二个方向努力:



1. 减少调用 IO 高的函数的次数或者使用并发

1. 优化 CPU 高的函数的代码 



!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548831836770-f407aed2-a46b-4ab2-99cf-e382711da99c.png\#align=left&display=inline&height=399&name=image.png&originHeight=798&originWidth=1343&size=149453&width=672\)



!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548832089515-c69865ce-f791-4dc7-b31d-6e6e96cd2950.png\#align=left&display=inline&height=427&name=image.png&originHeight=853&originWidth=1642&size=261904&width=821\)



!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548832432019-fa62708b-2020-4e68-b127-71c2b6704e6a.png\#align=left&display=inline&height=527&name=image.png&originHeight=1054&originWidth=2310&size=327720&width=1155\)

&lt;a name="7e2f6260"&gt;&lt;/a&gt;

\#\#\# 测试工具

根据日志自动声场测试用例, 但是目前的框架做的需要一些人为干预的步骤, 困难点正在解决中, 写完了链接会放在这

&lt;a name="1da60c9a"&gt;&lt;/a&gt;

\#\# 三. 重构手法\(原书第六-十一章\)

手法太多了, 记录一些有用的重构手法

&lt;a name="389f1950"&gt;&lt;/a&gt;

\#\#\# 1. 分解临时变量

同一个临时变量不是循环变量和用于收集计算结果的, 却被复制超过一次, 这时候第 n 次复制时可以另外定义一个变量用来说明, 这样可以增加可读性&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.loanrecord.impl.LoanDetailQueryComponentImpl\#getLoanDetail&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548916657083-dad590ee-30fe-4dee-80f2-8a68f4a5de83.png\#align=left&display=inline&height=71&name=image.png&originHeight=141&originWidth=684&size=23776&width=342\)



&lt;a name="869dfcf6"&gt;&lt;/a&gt;

\#\#\# 2. 封装集合

当一个函数返回一个 Collection 时, 返回集合的可读副本, 类内部提供添加/删除能力&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.common.model.ItemVO&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548917647287-74711a3e-f4da-4d36-8dd0-70f6fec67e51.png\#align=left&display=inline&height=341&name=image.png&originHeight=681&originWidth=866&size=107745&width=433\)&lt;br /&gt;渠道需要配置KV 格式数据时候的一个类, putValue 做的很好, 还需要优化将返回的集合变成不可变集合

&lt;a name="8ade592b"&gt;&lt;/a&gt;

\#\#\# 3. 以策略模式代替类型码

渠道层比较典型的案例是需要将不同种类的优惠券拼装成不同的视图返回给前端, 之前的代码是这么写的



\`\`\`java

// 提前收款卡分组

        List&lt;DiscountInfo&gt; tqskDiscountInfos = new ArrayList&lt;DiscountInfo&gt;\(\);

        // 折扣卡分组

        List&lt;DiscountInfo&gt; zkkDiscountInfos = new ArrayList&lt;DiscountInfo&gt;\(\);

        // 活动分组

        List&lt;DiscountInfo&gt; hdDiscountInfos = new ArrayList&lt;DiscountInfo&gt;\(\);

        //利息红包分组

        List&lt;DiscountInfo&gt; rateDiscountInfos = new ArrayList&lt;DiscountInfo&gt;\(\);



        List&lt;DiscountInfo&gt; discountInfos = consultResult.getDiscountInfos\(\);

        for \(DiscountInfo discountInfo : discountInfos\) {

            if \(DiscountTypeEnum.CAPITAL\_VOUCHER.getValue\(\)

                .equals\(discountInfo.getDiscountType\(\)\)\) {

                tqskDiscountInfos.add\(discountInfo\);

            } else if \(DiscountTypeEnum.DISCOUNT\_CAMP.getValue\(\)

                .equals\(discountInfo.getDiscountType\(\)\)\) {

                hdDiscountInfos.add\(discountInfo\);

            } else if \(DiscountTypeEnum.DISCOUNT\_VOUCHER.getValue\(\)

                .equals\(discountInfo.getDiscountType\(\)\)\) {

                zkkDiscountInfos.add\(discountInfo\);

            } /\*else if \(DiscountTypeEnum.COUPON\_VOUCHER.getValue\(\).equals\(discountInfo.getDiscountType\(\)\)\) {

                rateDiscountInfos.add\(discountInfo\);

            }\*/

        }

        discCardViews.addAll\(ListUtil.transform\(rateDiscountInfos, new Function&lt;DiscountInfo, BkmpDiscCardVO&gt;\(\) {

            @Override

            public BkmpDiscCardVO apply\(DiscountInfo discountInfo\) {

                BkmpDiscCardVO discountCardVO = new DiscountCardVO\(\);

                discountCardVO.setTitle\("工具利息红包"\);

                discountCardVO.setCanSelect\(discountInfo.isCanSelect\(\)\);

                discountCardVO.setDiscDefault\(discountInfo.isSelected\(\)\);

                discountCardVO.setDiscSeqno\(discountInfo.getDiscountId\(\)\);

                discountCardVO.setActivityType\(discountInfo.getDiscountType\(\)\);

                discountCardVO.setDesc\(discountInfo.getDescription\(\)\);

                discountCardVO.setDateDesc\("到期时间 " + DateTimeUtils.toYdianMdianDdianString\(

                    discountInfo.getEndDate\(\)\)\);

                discountCardVO.setTermDesc\("￥" + MoneyUtil.formatMoney\(discountInfo.getAvailableAmt\(\)\) + "元"\);

                return discountCardVO;

            }

        }\)\);



        if \(prodGroupComponent.isTqskLoanGroup\(portalRequest, pdCode\)\) {

            if \(!CollectionUtils.isEmpty\(tqskDiscountInfos\)\) {

                // 提前收款卡

                BkmpDiscCardVO discCardView = new BkmpDiscCardVO\(\);

                MultiCurrencyMoney voucherAvailableAmt = consultResult.getVoucherAvailabelAmt\(\)

                    .get\(DiscountTypeEnum.CAPITAL\_VOUCHER.getValue\(\)\);

                if \(voucherAvailableAmt != null\) {

                    discCardView

                        .setDiscSeqno\(VoucherDiscountInfoUtil.getCustIdOfVoucherDiscountInfos\(

                            voucherAvailableAmt.getAmount\(\), tqskDiscountInfos\)\);

                    discCardView.setTitle\("提前收款卡"\);

                    discCardView

                        .setDesc\("提前收款" + voucherAvailableAmt.getAmount\(\).toString\(\) + "元以内免手续费"\);

                    discCardView.setTermDesc\(

                        "提前收款" + voucherAvailableAmt.getAmount\(\).toString\(\) + "元以内@免手续费"\);

                    discCardViews.add\(discCardView\);

                }

            }

            if \(!CollectionUtils.isEmpty\(hdDiscountInfos\)\) {

                // 新手礼包活动

                for \(DiscountInfo discountInfo : hdDiscountInfos\) {

                    if \(discountInfo.getDiscountMode\(\).equals\(DiscountModeEnum.BY\_DAY.getCode\(\)\)\) {

                        continue;

                    }

                    if \(discountInfo.getDiscountMode\(\).equals\(DiscountModeEnum.BY\_TERM.getCode\(\)\)\) {

                        if \(discountInfo.isTerm\(\)\) {

                            continue;

                        }

                    }



                    BkmpDiscCardVO discCardView = new BkmpDiscCardVO\(\);

                    BigDecimal currFeeRate = discountInfo.getDiscountRate\(\).multiply\(fee\);

                    discCardView

                        .setDiscSeqno\(VoucherDiscountInfoUtil.getHdCustIdOfVoucherDiscountInfo\(

                            discountInfo.getDiscountId\(\), currFeeRate\)\);

                    //支付宝渠道+口碑

                    discCardView.setTitle\(discountInfo.getDiscountName\(\)\);

                    discCardView.setActivityType\(discountInfo.getDiscountType\(\)\);

                    discCardView.setTermDesc\(getNewTermDesc\(portalRequest, pdCode, discountInfo\)\);//TODO 需要重点改造

                    discCardView.setDesc\("新手首笔提前收款免手续费"\);

                    discCardView.setDateDesc\(this.getDateDesc\(discountInfo.getEndDate\(\)\)\);//TODO 此地方要注意

                    discCardView.setDiscDefault\(discountInfo.isSelected\(\)\);

                    discCardView.setCanSelect\(discountInfo.isCanSelect\(\)\);

                    discCardViews.add\(discCardView\);

                }

            }

            if \(!CollectionUtils.isEmpty\(zkkDiscountInfos\)\) {

                discCardViews

                    .addAll\(convertDiscountInfos2Views\(portalRequest, pdCode, zkkDiscountInfos\)\);

            }

        } else {

            if \(!CollectionUtils.isEmpty\(zkkDiscountInfos\)\) {

                discCardViews

                    .addAll\(convertDiscountInfos2Views\(portalRequest, pdCode, zkkDiscountInfos\)\);

            }

            if \(!CollectionUtils.isEmpty\(hdDiscountInfos\)\) {

                discCardViews

                    .addAll\(convertDiscountInfos2Views\(portalRequest, pdCode, hdDiscountInfos\)\);

            }

        }



        Iterator&lt;BkmpDiscCardVO&gt; iter = discCardViews.iterator\(\);

        BkmpDiscCardVO cardView = new BkmpDiscCardVO\(\);

        List&lt;BkmpDiscCardVO&gt; tempList = Lists.newArrayList\(\);

        //选择出所有可用的优惠

        while \(iter.hasNext\(\)\) {

            cardView = iter.next\(\);

            if \(!cardView.isCanSelect\(\)\) {

                tempList.add\(cardView.clone\(\)\);

                iter.remove\(\);

            }

        }

        discCardViews.addAll\(tempList\);

        return discCardViews;

\`\`\`



后来有了一个需求, 提前收款需要增加和其他贷款相似的费率打折活动, 这段代码我就有些改不动, 最初是想复制一下活动相关的代码, 结果发现下面的代码太长, 复制不动, 所以我就改造了下



&lt;a name="00e0c3f8"&gt;&lt;/a&gt;

\#\#\#\# 1. 编写开关编写新的逻辑



\`\`\`java

if \(bizDrmSwitchManager.useNewDiscount\(portalRequest.getUserRequest\(\).getIpId\(\)\)\) {

           return discountInfoConverter.convertDiscountInfos2Views\(portalRequest, pdCode, consultResult,

                fee\);

}

\`\`\`



&lt;a name="d34973ac"&gt;&lt;/a&gt;

\#\#\#\# 2. 根据注解读出所有的类型码对应的类并处理优惠列表



\`\`\`java

public class DiscountInfoConverterImpl implements DiscountInfoConverter {

    @Override

    public List&lt;BkmpDiscCardVO&gt; convertDiscountInfos2Views\(PortalRequest portalRequest, String pdCode, ConsultVO consultResult,

                                                           BigDecimal fee\) {

        List&lt;BkmpDiscCardVO&gt; bkmpDiscCardVO = new ArrayList&lt;&gt;\(\);

        //找到支持优惠券转换的类

        Map&lt;String, Object&gt; beanByAnnotation = SpringContextHolder.getBeanByAnnotation\(DiscountAnnotation.class\);

        List&lt;DiscountConvertStrategy&gt; discountConvertStrategies = beanByAnnotation.values\(\).stream\(\).map\(

            new Function&lt;Object, DiscountConvertStrategy&gt;\(\) {

                @Override

                public DiscountConvertStrategy apply\(Object object\) {

                    return \(DiscountConvertStrategy\) object;

                }

            }\).collect\(Collectors.toList\(\)\);



        discountConvertStrategies.forEach\(new Consumer&lt;DiscountConvertStrategy&gt;\(\) {

            @Override

            public void accept\(DiscountConvertStrategy discountConvertStrategy\) {

                //过滤出相应策略对应的优惠券列表

                List&lt;DiscountInfo&gt; discountInfos = discountConvertStrategy.filterTargetDiscount\(consultResult\);

                if \(ListUtil.isNotBlank\(discountInfos\)\) {

                    bkmpDiscCardVO.addAll\(

                        discountConvertStrategy.convertDiscountInfos\(portalRequest, pdCode, discountInfos, consultResult, fee\)\);

                }

            }

        }\);

        return bkmpDiscCardVO;

    }

}

\`\`\`



&lt;a name="4c45ba32"&gt;&lt;/a&gt;

\#\#\#\# 3. 默认策略实现



\`\`\`java

public abstract class DiscountConvertStrategy {



    @SofaReference

    private ProductIntrestComponent productIntrestComponent;



    /\*\*

     \* 过滤出当前处理器支持的优惠券列表

     \* @param consultResult

     \* @return

     \*/

    public List&lt;DiscountInfo&gt; filterTargetDiscount\(ConsultVO consultResult\) {

        DiscountAnnotation annotation = this.getClass\(\).getAnnotation\(DiscountAnnotation.class\);

        return consultResult.getDiscountInfos\(\).stream\(\).filter\(new Predicate&lt;DiscountInfo&gt;\(\) {

            @Override

            public boolean test\(DiscountInfo discountInfo\) {

                return StringUtils.equals\(annotation.value\(\).getValue\(\), discountInfo.getDiscountType\(\)\);

            }

        }\).collect\(Collectors.toList\(\)\);

    }



    /\*\*

     \* 将单个优惠券营销视图转为渠道层的视图

     \* @param portalRequest

     \* @param pdCode

     \* @param discountInfo

     \* @param consultResult

     \* @param fee

     \* @return

     \*/

    public abstract BkmpDiscCardVO convertDiscountInfo\(PortalRequest portalRequest, String pdCode, DiscountInfo discountInfo,

                                                       ConsultVO consultResult, BigDecimal fee\);



    /\*\*

     \* 将营销优惠卷列表转为渠道层视图列表, 具体的策略类可以重写该方法, 默认为将列表中的优惠券调用上面的单个转换方法并添加到列表中

     \* @param portalRequest

     \* @param pdCode

     \* @param discountInfo

     \* @param consultResult

     \* @param fee

     \* @return

     \*/

    public List&lt;BkmpDiscCardVO&gt; convertDiscountInfos\(PortalRequest portalRequest, String pdCode, List&lt;DiscountInfo&gt; discountInfo,

                                                     ConsultVO consultResult, BigDecimal fee\) {

        List&lt;BkmpDiscCardVO&gt; bkmpDiscCardVOs = new ArrayList&lt;&gt;\(\);

        return discountInfo.stream\(\).map\(new Function&lt;DiscountInfo, BkmpDiscCardVO&gt;\(\) {

            @Override

            public BkmpDiscCardVO apply\(DiscountInfo discountInfo\) {

                return convertDiscountInfo\(portalRequest, pdCode, discountInfo, consultResult, fee\);

            }

        }\).collect\(Collectors.toList\(\)\);

    }



&lt;a name="8e9d5db2"&gt;&lt;/a&gt;

\#\#\#\# 4. 具体策略重写抽象实现类

支付宝打折券:



\`\`\`java

public class DiscountConvertStrategyAlipayVoucherDiscImpl extends DiscountConvertStrategy {



    @SofaReference

    private ProdGroupComponent prodGroupComponent;



    @Override

    public BkmpDiscCardVO convertDiscountInfo\(PortalRequest portalRequest, String pdCode, DiscountInfo discountInfo,

                                              ConsultVO consultResult, BigDecimal fee\) {

        DiscountTxtComponent discountTxtComponent = prodGroupComponent.isTqskLoanGroup\(portalRequest, pdCode\)

            ? new DiscountTxtComponentServiceFeeImpl\(\) :

            new DiscountTxtComponentInterestImpl\(\);



        BkmpDiscCardVO discCardView = new BkmpDiscCardVO\(\);

        discCardView.setDiscSeqno\(discountInfo.getDiscountId\(\)\);

        discCardView.setTitle\(discountInfo.getDiscountName\(\)\);

        discCardView.setTermDesc\(this.obtainTermDesc\(portalRequest, pdCode, discountInfo, discountTxtComponent\)\);

        discCardView.setActivityType\(discountInfo.getDiscountType\(\)\);

        discCardView.setDateDesc\(this.obtainDateDesc\(discountInfo.getEndDate\(\)\)\);

        discCardView.setDiscDefault\(discountInfo.isSelected\(\)\);

        discCardView.setCanSelect\(discountInfo.isCanSelect\(\)\);

        discCardView.setDesc\(discountInfo.getDescription\(\)\);

        return discCardView;

    }

}

\`\`\`



提前收款卡:



\`\`\`java

public class DiscountConvertStrategyCapitalVoucherImpl extends DiscountConvertStrategy {



    @Override

    public BkmpDiscCardVO convertDiscountInfo\(PortalRequest portalRequest, String pdCode, DiscountInfo discountInfo,

                                              ConsultVO consultResult, BigDecimal fee\) {

        throw new BizRuntimeException\(ErrorCodeDefinitionEnum.CAPITAL\_VOUCHER\_NOT\_CONVERT\);

    }



    @Override

    public List&lt;BkmpDiscCardVO&gt; convertDiscountInfos\(PortalRequest portalRequest, String pdCode, List&lt;DiscountInfo&gt; discountInfo,

                                                     ConsultVO consultResult, BigDecimal fee\) {



        BkmpDiscCardVO discCardView = new BkmpDiscCardVO\(\);

        MultiCurrencyMoney voucherAvailableAmt = consultResult.getVoucherAvailabelAmt\(\)

            .get\(DiscountTypeEnum.CAPITAL\_VOUCHER.getValue\(\)\);

        if \(voucherAvailableAmt != null\) {

            discCardView

                .setDiscSeqno\(VoucherDiscountInfoUtil.getCustIdOfVoucherDiscountInfos\(

                    voucherAvailableAmt.getAmount\(\), discountInfo\)\);

            discCardView.setTitle\("提前收款卡"\);

            discCardView.setDiscDefault\(true\);

            discCardView

                .setDesc\("提前收款" + voucherAvailableAmt.getAmount\(\).toString\(\) + "元以内免手续费"\);

            discCardView.setTermDesc\(

                "提前收款" + voucherAvailableAmt.getAmount\(\).toString\(\) + "元以内@免手续费"\);

            discCardView.setActivityType\(DiscountTypeEnum.CAPITAL\_VOUCHER.getValue\(\)\);

            return ListUtil.toList\(discCardView\);

        }

        return ListUtil.emptyList\(\);

    }

}

\`\`\`

&lt;a name="5a9f8cfb"&gt;&lt;/a&gt;

\#\#\# 4. 引入 NULL 对象/特例对象

比如渠道聚合需要根据还款计划计算用户的余额或者逾期金额等, 代码大概如下:



\`\`\`java

for \(RepayPlanVO repayPlanVO : repayPlanVOs\) {

            totalSumAmt.addTo\(repayPlanVO == null ? BizConstant.ZERO\_MONEY : repayPlanVO.getTotalAmt\(\)\);

            totalPrinAmt.addTo\(repayPlanVO == null ? BizConstant.ZERO\_MONEY : repayPlanVO.getPrinAmt\(\)\);

            totalIntAmt.addTo\(repayPlanVO == null ? BizConstant.ZERO\_MONEY : repayPlanVO.getIntAmt\(\)\);

            totalChargeAmt.addTo\(repayPlanVO == null ? BizConstant.ZERO\_MONEY : repayPlanVO.getChargeAmt\(\)\);

            totalFineAmt.addTo\(repayPlanVO == null ? BizConstant.ZERO\_MONEY : repayPlanVO.getFineAmt\(\)\);

 }

\`\`\`



可以引用一个 NullRepayPlanVO 类, 继承 RepayPlanVO, 实现 Nullable 接口



\`\`\`java

public class NullRepayPlanVO extends RepayPlanVO implements Null {



    @Override

    public MultiCurrencyMoney getTotalIntAmt\(\) {

        return BizConstant.ZERO\_MONEY;

    }

}

\`\`\`



这样调用方的代码就不需要在判断空了, 直接在加入 list 的时候判断如果是 Null 则加入 NullRepayPlanVO, 操作的时候可以使用正常的对象



\`\`\`java

for \(RepayPlanVO repayPlanVO : repayPlanVOs\) {

            totalSumAmt.addTo\(repayPlanVO.getTotalAmt\(\)\);

            totalPrinAmt.addTo\(repayPlanVO.getPrinAmt\(\)\);

            totalIntAmt.addTo\(repayPlanVO.getIntAmt\(\)\);

            totalChargeAmt.addTo\(repayPlanVO.getChargeAmt\(\)\);

            totalFineAmt.addTo\(repayPlanVO.getFineAmt\(\)\);

 }

\`\`\`



&lt;a name="a8fbb69f"&gt;&lt;/a&gt;

\#\#\# 5. 将查询函数与修改函数分离

任何有返回值的函数, 都不应该有看得见的副作用&lt;br /&gt;com.mybank.bkloanportal.biz.shared.bkmportal.component.lendrecord.QueryLendRecordComponentImpl\#queryAllLoanRecordList&lt;br /&gt;!\[image.png\]\(https://intranetproxy.alipay.com/skylark/lark/0/2019/png/61637/1548922213703-177f0ede-bd50-480e-ac70-0f934ccc954a.png\#align=left&display=inline&height=107&name=image.png&originHeight=213&originWidth=752&size=60014&width=376\)&lt;br /&gt;查询全部贷款记录的时候, 由于要查询下一页, 下一页, 改变了入参, 应该函数内部建立一个新的临时变量承接入参, 以免对其他调用者产生影响



&lt;a name="2a5649cd"&gt;&lt;/a&gt;

\#\#\# 6. 塑造模板函数

一些类, 其中的某些函数以相同的顺序执行类似的操作, 但各个操作的细节上有所不同&lt;br /&gt;com.mybank.bkloanportal.biz.mportal.common.template.CommonLendFormProcessorTemplate&lt;br /&gt;比如用户进入支用表单的执行顺序如下, 基本相同, 只是信用贷款, 订单等文案, 利率计算等稍有不同, 抽出的支用表单模板如下:



\`\`\`java

 public &lt;P extends CommonLendFormRequest, C extends CommonLendFormContext&gt; CommonLendFormVO queryCommonLendFormVO\(

        PortalRequest portalRequest, P commonLendFormRequest, C commonLendFormContext,

        CommonLendFormProcessorCallBack&lt;P, C&gt; commonLendFormProcessorCallBack\) {



        commonLendFormProcessorCallBack.doValidate\(commonLendFormRequest\);

        CommonLendFormVO commonLendFormVO = new CommonLendFormVO\(\);

        //判断是否需要签署预数据授权合同

        if \(commonLendFormProcessorCallBack.needSignPreCreditContract\(commonLendFormContext, commonLendFormRequest\)\) {

            commonLendFormVO.setNeedSignPreCredit\(Boolean.TRUE\);

            return commonLendFormVO;

        }



        //判断用户是否需要升 A

        if \(commonLendFormProcessorCallBack.isNeedUpACerifyUser\(commonLendFormContext, commonLendFormRequest\)\) {

            commonLendFormVO.setAccountInfo\(new AccountInfoVO\(AccountStatusEnum.ACCOUNT\_NOT\_A\_LEVEL.getAccountStatusGroup\(\)\)\);

        }



        //获得贷款方案和产品优惠信息

        CommonProductionVO commonProductionVO = commonLendFormProcessorCallBack.buildCommonProductionVO\(commonLendFormContext,

            commonLendFormRequest\);

        commonLendFormVO.setCommonProductionVO\(commonProductionVO\);



        if \(commonProductionVO == null \|\| !commonProductionVO.getAdmit\(\)\) {

            commonLendFormVO.setBizErrorInfo\(commonLendFormProcessorCallBack.buildBizErrorInfo\(commonLendFormContext, commonProductionVO\)\);

            return commonLendFormVO;

        }



        //设置日利率, 还款方式, 提前还款状态标签

        commonProductionVO.setProductConfig\(

            commonLendFormProcessorCallBack.buildProductConfig\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);



        //查询合同列表

        commonLendFormVO.setLendContractVOs\(

            commonLendFormProcessorCallBack.buildLendContracts\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);



        commonLendFormVO.setLendContractGenernalTitle\(commonLendFormProcessorCallBack

            .buildLendContractGenernalTitle\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);

        //查询定制区域



        try {

            commonLendFormVO.setCustomizedArea\(

                commonLendFormProcessorCallBack.buildCustomizedArea\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);

        } catch \(Exception e\) {

            DdtyLogUtil.error\(portalRequest, DdtyLogUtil.DDTY\_LOGGER, "查询定制区域失败"\);

        }

        //如果是传入金额的

        if \(commonProductionVO.getLoanableAmt\(\) != null\) {

            //查询收款账号和还款账号

            DisburseAndRepayAccountList disburseAndRepayAccountList = commonLendFormProcessorCallBack.buildDisburseAndRepayAccounts\(

                commonLendFormContext, commonLendFormRequest\);

            commonLendFormVO.setDisburseAccountVOs\(disburseAndRepayAccountList.getDisburseAccountVOs\(\)\);

            commonLendFormVO.setRepayAccountVOs\(disburseAndRepayAccountList.getRepayAccountVOs\(\)\);

            commonLendFormVO.setRepayRemind\(

                commonLendFormProcessorCallBack.buildRepayRemind\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);



            //经营范围

            commonLendFormVO.setLendIndustryVO\(

                commonLendFormProcessorCallBack.buildIndustryVO\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);



            //出资机构

            commonLendFormVO.setLendOrgNames\(

                commonLendFormProcessorCallBack.buildLengOrgs\(commonLendFormContext, commonLendFormRequest, commonProductionVO\)\);

        }

        commonLendFormVO.setPermissionLimitVO\(commonLendFormProcessorCallBack.buildPermissionLimitVO\(commonLendFormContext\)\);

        return commonLendFormProcessorCallBack.buildCommonAttribute\(commonLendFormContext, commonLendFormRequest, commonLendFormVO\);

    }

\`\`\`



信用贷款和订单贷款或者订单驱动的表单页面的具体实现是体现在 commonLendFormProcessorCallBack 不同的实现类中



