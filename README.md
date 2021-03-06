利用Doc2Vec计算文档相似度
=============
算法思想
-------------
Doc2Vec模型基于Word2vec模型，并在其基础上增加了一个段落向量。
以Doc2Vec的C-BOW方法为例
1)	训练过程中新增了paragraph id，即训练语料中每个句子都有一个唯一的id。paragraph id和普通的word一样，也是先映射成一个向量，即paragraph vector。paragraph vector与word vector的维数虽一样，但是来自于两个不同的向量空间。在之后的计算里，paragraph vector和word vector累加或者连接起来，作为输出层softmax的输入。在一个句子或者文档的训练过程中，paragraph id保持不变，共享着同一个paragraph vector，相当于每次在预测单词的概率时，都利用了整个句子的语义。
2)	在预测阶段，给待预测的句子新分配一个paragraph id，词向量和输出层softmax的参数保持训练阶段得到的参数不变，重新利用梯度下降训练待预测的句子。待收敛后，即得到待预测句子的paragraph vector。

算法实现
-------------
利用Python的gensim.Doc2Vec接口进行模型训练，API如下：
<p>model = Doc2Vec(documents, size=100, window=8, min_count=5, workers=4)</p>
<p>documents为之前被处理好的TaggedDocument类型</p>	
<p>size是特征向量的维数</p>
<p>window是在文档中用于预测的预测单词和上下文单词之间的最大距离。</p>
<p>min_count是指低于此数量是词忽略不计</p>
<p>workers指线程数量</p>
<p>原始语料进行处理之后对模型进行训练，得到模型并进行存储。下一次使用直接调用节省时间</p>
'corpus_seg.txt'为分词好的语料，需要自行准备。

结果分析
-------------
<p>由于目前没有正式的语料库，我们只能通过肉眼进行准确率的评判。</p>
输入句子：
<p>'本院认为，被告人邱某以非法占有为目的，以暴力或以暴力胁迫手段抢劫他人财物；且以非法占有为目的，趁人不备夺取他人财物，数额较大，其行为已构成抢劫罪、抢夺罪。公诉机关指控的罪名成立。被告人邱某犯有数罪，依法实行数罪并罚。被告人邱某辩称及其辩护人提出被告人邱某没有以“掐死你”的语言威胁罗某，亦没有捂住易某的嘴巴，只是抢得金项链就跑，因而不构成抢劫罪，并有自首情节的辩护意见，经查，被害人罗某陈述被告人邱某以“掐死你”的语言威胁，易某陈述邱某捂住其嘴巴，而后抢走金项链、金吊坠，被告人邱某在侦查阶段的供述亦与被害人陈述相吻合，且被告人邱某系当场被公安机关抓获后才交待抢劫犯罪事实，当庭对抢劫予以否认不符合自首的构成要件，因此本院对被告人邱某辩称及其辩护人提出的不构成抢劫罪及有自首情节的意见不予采纳；但对被告人邱某的辩护人提出赔偿了被害人罗某的损失，取得了罗的谅解，依法可酌情从轻处罚的辩护意见，本院予以采纳。依照《中华人民共和国刑法》第二百六十三条、第二百六十七条第一款、第六十一条、第六十九条、第五十二条、第五十三条之规定，判决如下：'
<p>返回前十个相似的句子，如下：
<p>本院认为 被告人 邱某 非法占有 目的 暴力 暴力 胁迫 手段 抢劫 人财物 非法占有 目的 趁人 不备 夺取 人财物 数额较大 行为 已 构成 抢劫罪 抢夺罪 公诉 机关 指控 罪名 成立 被告人 邱某 犯 有数 罪 依法 实行 数罪并罚 被告人 邱某 辩称 辩护人 提出 被告人 邱某 掐死 语言 威胁 罗某 亦 捂住 易 嘴巴 抢 金项链 跑 构成 抢劫罪 自首 情节 辩护 意见 经查 被害人 罗某 陈述 被告人 邱某 掐死 语言 威胁 易 陈述 邱某 捂住 嘴巴 抢走 金项链 金吊 坠 被告人 邱某 侦查 阶段 供述 亦 被害人 陈述 相吻合 被告人 邱 某系 公安机关 抓获 后 交待 抢劫犯罪 事实 抢劫 予以 否认 符合 自首 构成 要件 本院 被告人 邱某 辩称 辩护人 提出 构成 抢劫罪 自首 情节 意见 不予 采纳 被告人 邱某 辩护人 提出 赔偿 被害人 罗某 损失 取得 罗 谅解 依法 酌情 处罚 辩护 意见 本院 予以 采纳 中华人民共和国 刑法 第二百六十三条 二百六十 七条 第一款 第六十一条 第六十九条 第五十二 条 第五十三条 规定 判决  0.677473783493042 148 0
<p>本院认为 被告人 罗某 非法占有 目的 采用 暴力手段 劫取 人财物 行为 已 构成 抢劫罪 公诉 机关 指控 成立 应予 支持 被告人 罗某 归案 后 如实 供述 罪行 依法 处罚 辩护人 提出 被告人 罗 某系 初犯 如实 供述 罪行 依法 处罚 辩护 意见 予以 采纳 中华人民共和国 刑法 第二百六十三条 第六十七条 第三款 第五十二 条 第五十三条 规定 判决  0.48076367378234863 53 10690
<p>本院认为 被告人 邱某 喻雍强 潘浩以 非法占有 目的 采用 暴力手段 劫取 人财物 行为 已 构成 抢劫罪 公诉 机关 指控 成立 应予 支持 被告人 邱某 喻雍强 潘浩 归案 后 如实 供述 罪行 处罚 被告人 邱某 协助 公安机关 抓获 同案犯 系 立功 依法 处罚 辩护人 分别 提出 被告人 邱某 喻雍强 潘浩系 初犯 如实 供述 罪行 处罚 辩护 意见 予以 采纳 中华人民共和国 刑法 第二百六十三条 第二十五条 第一款 第六十七条 第三款 第六十八条 第五十二 条 第五十三条 第六十四 条 规定 判决  0.4779859185218811 72 14493
<p>本院认为 被告人 邱某 非法占有 目的 采用 暴力 胁迫 手段 劫取 人财物 行为 已 构成 抢劫罪 公诉 机关 指控 罪名 成立 被告人 邱某 归案 后 如实 供述 罪行 处罚 案发后 赃物 已 追回 被告人 邱某 酌情 处罚 中华人民共和国 刑法 第二百六十三条 第六十七条 第三款 第五十二 条 第五十三条 规定 判决  0.47012799978256226 46 38678
<p>本院认为 被告人 罗某 非法占有 目的 结伙 采用 暴力 胁迫 手段 劫取 公民 财物 行为 已 构成 抢劫罪 公诉 机关 指控 罪名 成立 被告人 罗某 已 着手 实行 犯罪 意志 以外 原因 未 得逞 犯罪 未遂 未遂犯 依法 遂 犯 减轻 处罚 共同犯罪 中 被告人 罗某 次要 作用 系 从犯 依法 应当 减轻 处罚 被告人 罗某 归案 后 如实 供述 罪行 庭审 中 自愿 认罪 依法 处罚 被告人 犯罪 事实 性质 情节 社会 危害 程度 中华人民共和国 刑法 第二百六十三条 第二十五条 第一款 第二十三条 第二十七条 第六十七条 第三款 最高人民法院 适用 财产 刑 若干 问题 规定 第二条 第一款 规定 判决  0.46559205651283264 94 47552
<p>本院认为 被告人 罗某 非法占有 目的 采用 暴力手段 劫取 公民 财物 行为 已 构成 抢劫罪 被告人 罗某 已经 着手 实施 犯罪 意志 以外 原因 未能得逞 属 犯罪 未遂 遂 犯 减轻 处罚 归案 后能 如实 供述 罪行 依法 处罚 综上 被告人 罗某 减轻 处罚 常州市 武进区 人民检察院 起诉 指控 被告人 罗某 犯有 抢劫罪 罪名 成立 应予 支持 公诉人 建议 被告人 罗某 判处 有期徒刑 一年 六个月 三年 六个月 处罚金 量刑 意见 恰当 本院 亦予 采纳 严肃 法制 惩治 罪犯 保障 公民 人身 财产权利 受 侵犯 维护 社会治安 秩序 中华人民共和国 刑法 第二百六十三条 第二十三条 第六十七条 第三款 第五十二 条 第五十三条 规定 判决  0.46517154574394226 97 12509
<p>本院认为 被告人 罗某 非法占有 目的 伙同他人 采取 暴力手段 劫取 公民 财物 行为 已 构成 抢劫罪 共同犯罪 中 被告人 罗某 积极 主要 作用 系 主犯 被告人 罗 某系 故意犯罪 判处 有期徒刑 以上 刑罚 犯罪分子 刑罚 执行 完毕 五年 以内 再犯 应当 判处 有期徒刑 以上 刑罚 抢劫罪 系 累犯 依法 予以 处罚 被告人 罗某 到案 后能 如实 供述 犯罪事实 具有 坦白 情节 依法 予以 处罚 公诉 机关 建议 判处 被告人 罗某 三年 六个月 五年 六个月 有期徒刑 处罚金 量刑 建议 恰当 本院 予以 采纳 中华人民共和国 刑法 第二百六十三条 第二十五条 第一款 第二十六条 第一款 第四款 第六十五条 第一款 第六十七条 第三款 第五十二 条 规定 判决  0.4499894976615906 97 18130
<p>公诉 机关 指控 2012 年 月 日 14 时许 被告人 罗 某行 潜入 宝安区 松岗 塘下 涌富塘路 服装店 行窃 时 店主 王某 发现 罗某 见状 逃跑 王某 后 追赶 逃跑 过程 中 罗 某持 螺丝刀 威胁 王某 后 制服 公诉 机关 法庭 提供 相应 证据 认为 被告人 罗某 行为 已 构成 抢劫罪 请求 依法 判处  0.4481847584247589 55 25432
<p>本院认为 被告人 邱某 伙同他人 采用 暴力手段 劫取 人财物 行为 已 构成 抢劫罪 公诉 机关 指控 被告人 犯 抢劫罪 罪名 成立 被告人 辩护人 提出 被告人 系 未成年人 应当 减轻 免除 处罚 且系 初犯 认罪态度 好 赃物 已 追缴 返还 被害人 未 被害人 造成 经济损失 酌情 处罚 经查 属实 本院 予以 采信 被告人 邱某 审判 时 尚未 十六周岁 贯彻 教育 为主 惩罚 为辅 原则 被告人 邱某 一次 改过自新 机会 适用 缓刑 不致 再 危害 社会 适用 缓刑 本案 事实 情节 中华人民共和国 刑法 第二百六十三条 第十七条 第二款 第三款 第五十二 条 七十二条 第一款 第三款 第七十三 条 第二款 第三款 规定 判决  0.4446118474006653 95 17118
<p>本院认为 被告人 罗某 非法占有 目的 驾驶 机动车 强行 夺取 人财物 行为 已触犯 中华人民共和国 刑法 第二百六十三条 构成 抢劫罪 依法 应当 追究其 刑事责任 四川省 双流县 人民检察院 起诉 指控 事实清楚 罪名 成立 本院 予以 支持 被告人 罗某 驾驶 车辆 强行 夺取 人财物 时 被害人 放手 采取 强拉硬 拽 方法 劫取 财物 行为 符合 抢劫罪 构成 要件 被告人 罗某 提出 行为 构成 抢劫罪 辩护 意见 本院 予以 采纳 被告人 罗莫在 实施 抢劫 过程 中 未劫取 财物 未 造成 受害人 轻伤 以上 伤害 后果 抢劫 未遂 本院 量刑 时 依法 被告人 罗某 处罚 被告人 罗某 归案 后 如实 供述 犯罪事实 认罪态度 好 本院 量刑 时 酌情 处罚 被告人 罗某 犯罪事实 犯罪 性质 情节 社会 危害 程度 中华人民共和国 刑法 第二百六十三条 第二十三条 第五十二 条 第五十三条 规定 判决  0.44266051054000854 120 4711
		
<p>肉眼观察效果较好。
