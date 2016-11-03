## 程序设计组件化方案
###抛出观点
```
* View层 : 分层设计 组件化开发
* Model层: 让数据来做视图掌控者
* Controller层 : 减少继承深度 降低耦合
* 工具链层 : 降低版本依赖
* 业务层   : 当你把以上任务完成之后 业务层简单明了
```
-
###组建化你的视图(View)
```
视图是前端开发最直观的展示，涉及到页面方方面面，聊天，列表，图片，网页等,我们自定义聊天页面，定义商品展示页面，定义图片展示页面，瀑布展示页面，后来才发现原来我们做的页面都是一次性工作，版本升级，页面改版，之前所有的工作都没了。
```
####那么如何去组件化设计你的前端页面呢？
首先来看两个有代表性的前端页面，最近双11，小编截图了天猫双11首页设计图(由于不便于区分模块，小编把页面调成了黑白)。简单分析下。

![GitHub set up](http://www.goofyy.com/blog/wp-content/uploads/2016/11/tmall_scr-2.jpeg)

我们可以察觉其实天猫前端页面其实有N多个模块组成,例如
```
* 模块1是热销商品展示页面,这个页面可能有运营添加部分在天猫付费推广的商品
* 模块2是商品分类 
* 模块3是双11会场商品精选 
* 模块4是双11商品推荐 可能针对用户画像推荐的商品
* 模块5是双11底部tabbar 双11节日图片icon
```
整个天猫双11页面,直观感受都是由不同的模块开发，开发者在开发天猫首页的过程当中，没必要关注整个页面是什么样子的，任务按模块分案发给不同的开发者。整个双11首页可能一共有100个模块，天猫首页只能显示20个页面，然后由运营来统筹管理这20个页面，例如在双11前15天的时候，开始宣传双11节日，这时候首页主要放一些双11介绍大图，在进入11月的时候，运营更新抢红包页面，这样子模块化视图开发，不单单利于页面布局，而且利于运营推广来做活动的分发和管理。    

简单举一个反例，假如双11之前没有活动，页面写死。首页banner图 + 推荐的店铺 + 推荐的商品 + 热门商品，然后双11前15天，开始开发双11的版本，然后月底苹果审核突然由于某个原因拒绝上架，这时候，双11活动没了，剁手党们肿么办？


####同时组建化开发视图利于功能的维护和拓展
这个说辞比较适合的方案是QQ的聊天的方案：可以先看下QQ聊天界面视图

![GitHub set up](http://www.goofyy.com/blog/wp-content/uploads/2016/11/qq_scr.jpeg)

```
* 模块1 系统消息view (系统时间)
* 模块2 一个空白的view
* 模块3 gif动态表情的view
* 模块4 文字内容的view
* 模块5 语音聊天View
* 其他未展示出来的模块 小视频模块 红包模块 等
```

######看到如上的功能简单模拟一个开发场景
``` 
《第一个月》  
产品经理说 :"我想要一个聊天功能，只要有文字聊天就可以了"    
程序员说："好"    

《第二个月》   
产品经理说 "别的公司都有语音了，我们也加一个发语音功能吧"  
程序员皱皱眉头还是答应了

《第三个月》   
产品经理跪下说："大爷，我们加一个发图片的功能吧"  
程序员想，好歹都跪了，干吧。

《第四个月》   
产品经理来到程序员面前。还未开口     
程序员："滚"
```     

在开发者开发项目过程当中，随着项目的开发，项目功能的拓展似乎是必然的是，因为原先的需求不足以支撑现在的服务，而在原来的业务和代码逻辑到底能不能支撑功能拓展呢。这是一个值得商榷的问题。

例如QQ的聊天页面，可能随着功能的开发，慢慢增加了图片，表情，语音，视频，红包等功能。如何能够去设计一个拓展性强的视图呢。

答：组件化开发

首先大家都能看到这个页面可以用TableView来做，每一条信息都是一个cell，那么我们可以针对不同的消息类型创建不同的cell。

```
* 普通文本消息 - 纯文本cell   NormalTextCell
* 语音消息    - 语音消息cell  AudioCell
* 视频消息    - 视频消息cell  VideoCell
* 图片消息    - 图片消息cell  PictureCell
* GIF表情消息 - GIF消息cell  GifCell
* 红包消息    - 红包消息cell  RedWalletCell
* 系统消息    - 系统消息cell  SystemMessageCell
* 空白消息    - 空白图cell    BlankCell
```
针对不同的消息类型，在当前视图当中追加上响应的cell即可，每个模块相互隔离，不相互关联，同时响应模块视图进行更改的时候，找到对应的模块也是很简单的。怎么样，是不是很爽。部分代码如下。
```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    
    id obj = self.models[indexPath.row];
    
    if ([obj isMemberOfClass:[ChatMessageCellModel class]]) {
        
        HRChatMessageCell *cell = [tableView dequeueReusableCellWithIdentifier:[ChatMessageCellModel identifier] forIndexPath:indexPath];
        [cell setBackgroundColor:[UIColor clearColor]];
        cell.model = obj;
        
        return cell;
        
    }
    
    if ([obj isMemberOfClass:[HRBaseTableViewCellModel class]]) {
        HRBaseTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:[HRBaseTableViewCellModel identifier] forIndexPath:indexPath];
        [cell setBackgroundColor:[UIColor clearColor]];
        
        return cell;
    }
    
    if ([obj isMemberOfClass:[HRCenterSystemMessageCellModel class]]) {
        HRCenterSystemMessageCell *cell = [tableView dequeueReusableCellWithIdentifier:[HRCenterSystemMessageCellModel identifier] forIndexPath:indexPath];
        cell.model = obj;
        [cell setBackgroundColor:[UIColor clearColor]];
        return cell;
    }
    
    if ([obj isMemberOfClass:[HRRemoteSpecialCellModel class]]) {
        HRRemoteSpecialCell *cell = [tableView dequeueReusableCellWithIdentifier:[HRRemoteSpecialCellModel identifier] forIndexPath:indexPath];
        cell.model = obj;
        [cell setBackgroundColor:[UIColor clearColor]];
        return cell;
    }
    
    return nil;
}

```
根据获取到不同的消息Model去返回不同的cell.


