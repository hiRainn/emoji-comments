### emoji-comments


dependencies :{
	"jquery": "^3.5.1",
	"ant-design-vue": "^1.6.4",
}

This component relies slightly on the above dependencies, if it needs to be modified，<a href="#change">Click here</a>

### [中文文档](https://github.com/hiRainn/emoji-comments/blob/master/README_ZN.md)


### How to use  


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
	ShowText="click to view all {Number} comments" //the text template that you click to expand comments
	HideText="click to hide comments"  //the text template that you click to hide comments
	repeatType="cancle"
	:allowComment="Boolean(article.allow_comment)"
	:showReport="true"
	:reportText="'report'" 
	:showName="true" 
	:showEmail="true"
	:replayText="'replay'" 
	:title="comment_number?('total ' + comment_number + ' comments'):'No comments yet,come to post the first~'"
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

### ps:don't worry about the chinese.It's just a style template.You can replace it to any language you want

![](https://oscimg.oschina.net/oscnet/up-ce86ea6454df8cb4f566445af0e02fff2e2.png)
![](https://oscimg.oschina.net/oscnet/up-0b3d1868ebe59f5fc3173a4f1dfec37497b.png)
![](https://oscimg.oschina.net/oscnet/up-7060375dbfbe2e163abf18cad0ab6706ce3.png)
![](https://oscimg.oschina.net/oscnet/up-cace07e39075052db760e1521d39c5feca1.png)
![](https://oscimg.oschina.net/oscnet/up-c40aec8c857181d358001911ace9cdb3f23.png)

![](https://oscimg.oschina.net/oscnet/up-91a72626557cf3b1f65414faf5aa7af2496.png)
![](https://oscimg.oschina.net/oscnet/up-b086cd061b6baa94822a18cebeb8516f435.png)
![](https://oscimg.oschina.net/oscnet/up-beb6172b37b35e86444b6b3a8342200d1c6.png)
![](https://oscimg.oschina.net/oscnet/up-7365255ebd4f76c200b6de1407ba0643ae7.png)

### name and email can be hidden in the form by setting property showName,showEmail to false.


### props

|props|meaning|data type|defallt|
|-|-|-|-|
|list|comment list|Array|[]|
|title|title of the comment area|String|'',ps:like 'total xx comments'|
|AdminText|mark-text of author|String|'author'|
|AdminTagColor|color of mart-text，it can be hex or color words|String|'8CC5FF'|
|AnonymousText|name display when not filled in|String|'Anonymous'|
|tipText|tips at the top of the form |String|'your name and email will not be published,there are the unique key to get your comments'|
|nameText|name|String|'name'|
|emailText|email|String|'email'|
|hideNumber|the number of comments when it will be hidden|Number|3|
|ShowText|the text template that you click to expand comments|String|"click to view all {Number} comments"，{Number} is a placeholder, and case must be consistent|
|HideText|the text template that you click to hide comments|Strung|'click to hide comments'|
|buttonText|the text on the submit button|String|'post'|
|cancleText|the text on the cancle button|String|'cancle'|
|saveText|the text beside the save-checkbox|String|'save in the browser'|
|content|comtent of textarea|String|''|
|reportText|the text of report link text|String|'report'|
|replayText|the text of replay link text|String|'replay'|
|PcAnchor|anchor'name of the comment|String|'PcAnchor'|
|PhoneAnchor|anchor'name of comment|String|'PhoneAnchor'|
|showReport|whether to show report|Boolean|true|
|showReplay|whether to show replay|Boolean|true|
|showLike|whether to show like|Boolean|true|
|showUnlike|whether to show dislike |Boolean|true|
|showName|whether to show 'name' in the comment's form|Boolean|false|
|showEmail|whether to show 'email' in the comment's form|Boolean|false|
|showTip|whether to show tips at the top of comment's form|Boolean|false|
|allowComment|whether to allow comment|Boolean|true|
|AnimateOn|whether to open animate of clicking like or dislike|Boolean|true|
|likeColor|color of like，it can be hex or color words|String|'red'|
|unlikeColor|color of dislike，it can be hex or color words|String|'gray'|
|repeatType|process mode of repeating click report/like/dislike |String，'cancle','prevent'，|'prevent'|



### data struct


```vue
list:[
	{
		data:{
			id:int,  //require，comment's id
			name:string, //require，AnonymousText when empty
			content:string,//require
			pid:int,  //require 
			created_at:int|string,  //require ,comment's time
			like_number:int, //number of like
			unlike_number:int, //number of dislike
			is_admin:int|boolean,  //determine whether is admin/author
			like:int|boolean ,//Whether the current user has liked this comment，you can get it in function clickLike, row.data.like
			unlike:int|boolean, //Whether the current user has disliked this comment，you can get it in function clickUnlike, row.data.unlike
			report:int|boolean //Whether the current user has reported this comment，，you can get it in functuon clickReport,row.data.report
		},
		children:[list]
	},
	{...}...
]
```


### function(callback)

all functions but 'submit' need to callback boolean result,[clike to view demo.vue](https://github.com/hiRainn/emoji-comments/blob/master/src/demo.vue)


|function Name|meaning|params|use|
|-|-|-|-|
|clickReport|report|row，callback|row is the object being manipulated ，callback(true) when success|
|clickLike|like|row，callback|row is the object being manipulated ，callback(true) when success|
|clickUnlike|dislike|row，callback|row is the object being manipulated ，callback(true) when success|
|canclekReport|cancle report|row，callback|row is the object being manipulated ，callback(true) when success|
|cancleLike|cancle like|row，callback|row is the object being manipulated ，callback(true) when success|
|cancleUnlike|cancle dislike|row，callback|row is the object being manipulated ，callback(true) when success|
|submit|post comment|form，callback|form is the content of comment's form，callback(data) when success. data structure is below |

```vue 
data:{
	id:int,
	name:String,
	pid:int,
	created_at:String
	content:String
}
```



<h4 id='change'>Modify the dependencies</h4>


### antDV 

1. a-col/a-row are used for mobile phone/Pc/Pad style adaption 
2. icon，a-icon in sub-component of commentList/commentListPhone
3. form:a-form
4. a-drawer in sub-component in mentListPhone

#### jquery 

1. click on the reply text positioning and scrolling animation effect in the index.vue
2. aminate of like/dislike in sub-component of commentList/commentListPhone
