## 程序设计组件化方案
###抛出观点
```
* View层 : 分层设计 组件化开发
* Model层: 让数据来做视图掌控者
```
-
###组建化你的视图(View)
```
视图是前端开发最直观的展示，涉及到页面方方面面，聊天，列表，图片，网页等,我们自定义聊天页面，
定义商品展示页面，定义图片展示页面，瀑布展示页面，后来才发现原来我们做的页面都是一次性工作，
版本升级，页面改版，之前所有的工作都是枉然了。
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

####Web组件化开发
例如天猫双11的图用web组建化开发可以展示为
```
* 首页Banner图
* banner 图
* 多个可以水滚动的 banner
* 为你推荐 (其实也是一个banner图)
* 底部tabbar
```
如何来做这样一个网站呢。基于模块开发。每个模块独立成一个主体，以后在添加或者删除响应的模块的时候，相对会简单很多。部分代码可以展示如下
```
{{extentd("sloution://base/")}}

{{#block("bidy")}}

{{use ("muli://headbanner/",$data.modules[1])}}
{{use ("muli://banner/",$data.modules[2])}}
{{use ("muli://multi-banner/",$data.modules[3])}}
{{use ("muli://recommendbanner/",$data.modules[1])}}
{{use ("muli://floor/",$data.modules[4])}}
{{use ("muli://bottomTabbar/",$data.modules[5])}}

```

###让数据来做视图掌控者
数据是一个APP的灵魂，既然是灵魂就应该充当起灵魂的责任，就要负责起整个前端页面的整个饮食起居，不然前端怎么知道我收到的数据是普通文本消息而不是一个视频消息呢。所以视图要和数据绑定。还是上代码
####数据和视图绑定
```
@interface ChatMessageCellModel : HRBaseTableViewCellModel

@property(nonatomic,strong) HRMessageModel  *messageModel;

@property(nonatomic,copy) void(^tapImageHandle)(HRMessageModel *model,UIImageView *imageView);

@property(nonatomic,copy) void(^tapUserImageHandle)(HRMessageModel *model);

@end


@interface HRChatMessageCell : HRBaseTableViewCell

@property(nonatomic,strong) ChatMessageCellModel  *model;

+(CGFloat)cellHeightWithTableView:(UITableView *)tableView cellModel:(ChatMessageCellModel *)cellModel;


@end

```

ChatMessageCellModel和HRChatMessageCell进行绑定，

模型 | 视图
---- | ---
ChatMessageCellModel | HRChatMessageCell
NormalTextCellModel | NormalTextCell
AudioCellModel | AudioCell
...|...
这样当进入到一个聊天页面时候，从网络或者本地load了50条聊天记录，转化为对应的模型时，在 ```cellForRowAtIndexPath ```里针对对应的model展示响应的cell视图

####数据控制前端视图展示
类似天猫首页的活动页面通过运营的控制可以实现灵活的热更新，在不用APP上架更新的情况下，动态更新首页视图。类似如下。


#####非双11首页展示数据
```
{
	data:{
		//首页banner图
		banner:{
			...
		},
		//首页菜单
		menu:{
			...
		},
		//推荐店铺
		recommendShop:{
			...
		},
		//推荐商品
		recommendCommodity:{
			...
		},
		//热门店铺
		hotShop:{
			...
		},

	},
	code:1
}
```

双11首页展示数据
```
{
	data:{
		// 双11抢红包模块
		shuang11RedWallet:{
			...
		},
		//双11 热店
		shuang11HotShop:{
			...
		},
		//双11 热购商品
		shuang11HotCommodity:{
			...
		},
		//双11精选
		shuang11JingXuan:{
			...
		},
		//双11帮你挑
		shuang11HelpUChoose:{
			...
		},
		//双11 个人推荐
		shuang11Recommend:{
			...
		},
		//双11 tabbar icon图标
		shuang11Icon:{

		}
	},
	code:1
}
```
根据这些相应的数据，前端可以根据相应的逻辑构建响应的视图界面效果，对前端进行模块化更新。



