### emoji-comments


dependencies :{
	"jquery": "^3.5.1",
	"ant-design-vue": "^1.6.4",
}

该组件轻度依赖上述依赖，如果需要修改，则 <a href="#change">点击此处</a>

### 使用

```vue
	<comment 
	@submit="comment"
	@clickUnlike="clickUnlike" 
	@clickLike="clickLike" 
	@clickReport="clickReport"
	@cancleReport="cancleReport"
	@cancleLike="cancleLike"
	@cancleUnlike="cancleUnlike"
	:hideNumber="3"
	ShowText="点击查看 {Number} 条评论"
	HideText="收起评论"
	repeatType="cancle"
	:allowComment="Boolean(article.allow_comment)"
	:showReport="true"
	:reportText="'举报'" 
	:showName="true" 
	:showEmail="true"
	:replayText="'回复'" 
	:title="comment_number?('当前共 ' + comment_number + ' 条评论'):'当前还没有评论，快来评论第一条吧~'"
	:list="comment_list" 
	/>
```

```vue
import Comment from '@/components/Comment'
export default {
	components: {
		Comment,
	},
}

```

![](https://oscimg.oschina.net/oscnet/up-ce86ea6454df8cb4f566445af0e02fff2e2.png)
![](https://oscimg.oschina.net/oscnet/up-0b3d1868ebe59f5fc3173a4f1dfec37497b.png)
![](https://oscimg.oschina.net/oscnet/up-7060375dbfbe2e163abf18cad0ab6706ce3.png)
![](https://oscimg.oschina.net/oscnet/up-cace07e39075052db760e1521d39c5feca1.png)
![](https://oscimg.oschina.net/oscnet/up-c40aec8c857181d358001911ace9cdb3f23.png)

![](https://oscimg.oschina.net/oscnet/up-91a72626557cf3b1f65414faf5aa7af2496.png)
![](https://oscimg.oschina.net/oscnet/up-b086cd061b6baa94822a18cebeb8516f435.png)
![](https://oscimg.oschina.net/oscnet/up-7365255ebd4f76c200b6de1407ba0643ae7.png)

表单的姓名于与邮箱可以隐藏，通过设置showName与showEmail为false，即不获取这两个属性


### 属性 

|属性名称|内容|类型|默认值|
|-|-|-|-|
|list|评论列表|Array|[]|
|title|评论区标题|String|'',ps:一般传入类似'总共xx条评论'|
|AdminText|作者标注名称|String|'author'|
|AdminTagColor|作者标示的颜色，可以是16进制数，也可以是颜色单词|String|'8CC5FF'|
|AnonymousText|未填写姓名时匿名名称|String|'匿名用户'|
|tipText|评论表单区提示文字|String|'你的邮箱不会显示，姓名与邮箱将作为唯一的key以便获取回复信息，建议勾选保存在浏览器中'|
|nameText|姓名|String|'姓名'|
|emailText|邮箱|String|'邮箱'|
|hideNumber|手机端子评论超过多少条隐藏|Number|3|
|ShowText|手机端子评论点击显示文字模板|String|"点击展开全部{Number}条回复"，{Number}为占位符，大小写必须一致，必须|
|HideText|手机端子评论收起展开回复|Strung|'点击收起回复'|
|buttonText|提交按钮名称|String|'提交'|
|cancleText|取消按钮名称|String|'取消'|
|saveText|保存checkbox文字|String|'保存在浏览器中'|
|content|回复框内容|String|''|
|reportText|举报按钮文字|String|'report'|
|replayText|回复按钮文字|String|'replay'|
|showReport|是否显示举报按钮|Boolean|true|
|showReplay|是否显示回复按钮|Boolean|true|
|showLike|是否显示点赞|Boolean|true|
|showUnlike|是否显示踩|Boolean|true|
|showName|回复表单是否显示姓名|Boolean|false|
|showEmail|回复表单是否显示emai|Boolean|false|
|showTip|回复表单是否显示提示|Boolean|false|
|allowComment|是否允许评论|Boolean|true|
|AnimateOn|点赞与踩的动画加减一效果是否开启|Boolean|true|
|likeColor|点赞的颜色，可以是16进制数，也可以是颜色单词|String|'red'|
|unlikeColor|踩的颜色，可以是16进制数，也可以是颜色单词|String|'gray'|
|repeatType|重复点击举报、踩、攒时的处理方式|String，'cancle','prevent'，cancle为取消，prevent则不作任何处理|'prevent'|



### 数据结构

list数据要求

```vue
list:[
	{
		data:{
			id:int,  //必要，代表评论id
			name:string, //必要，代表回复人名称，为空则显示为AnonymousText
			content:string,//必要 代表回复内容
			pid:int,  //必要  代表回复父id
			created_at:int|string,  //必要 回复时间
			like_number:int, //被点赞数
			unlike_number:int, //被踩数
			is_admin:int|boolean,  //判断是否为作者(管理员)
			like:int|boolean ,//表示当前用户是否已经点赞过这条评论，获取为clickLike中row.data.like
			unlike:int|boolean, //表示当前用户是否已经点踩过这条评论，获取为clickUnlike中row.data.unlike
			report:int|boolean //表示当前用户是否举报过这条评论，获取为clickReport中row.data.report
		},
		children:[list]
	},
	{...}...
]
```

*在显示上采用的是层级递归，所以即使有无限级children，也可以显示，但是不建议这么做，因为层层右移，所以当级数过多会导致页面样式出现不可控的混乱*

### 方法 

所有方法除submit外均需要回调布尔值，回调true则继续执行下一步操作，不会使用回调的话，请看例子[demo.vue](https://github.com/hiRainn/emoji-comments/blob/master/src/demo.vue),会简化到只包含调用的4个方法

submit函数需要执行回调cab({id:new_id,content:htmlEscape(form.content),name:form.name,created_at:created_at})



|方法名称|作用|参数|参数含义|
|-|-|-|-|
|clickReport|举报|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|clickLike|点赞|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|clickUnlike|踩|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|canclekReport|取消举报|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|cancleLike|取消点赞|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|cancleUnlike|取消踩|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|submit|提交评论|form，cab|form为表单内容，cab为回调函数|



<h4 id='change'>修改依赖</h4>

##### 改组件依赖ant-design-vue 与 jquery，如需使用element或其他组件，改动不大，改动如下：

### antDV 需要修改的属性

1. a-col用于适配手机与pc评论列表页的响应式样式，修改span即可
2. 子组件中点赞与踩的icon，a-icon
3. 表单a-form
4. CommentListPhone组件中抽屉

#### jquery 需要修改的属性

1. 点击回复时的回复窗口定位以及滚动动画效果，位于index.vue中
2. commentList与commentListPhone点赞以及踩的动画效果需要删除
