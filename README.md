### emoji-comments


dependencies :{
	"jquery": "^3.5.1",
	"ant-design-vue": "^1.6.4",
}

该组件轻度依赖上述依赖，如果需要修改，则 <a href="#change">点击此处<.a>


##### 属性 

|属性名称|内容|类型|默认值|
|-|-|-|-|
|list|评论列表|Array|[]|
|AnonymousText|未填写姓名时匿名名称|String|'匿名用户'|
|tipText|评论表单区提示文字|String|'你的邮箱不会显示，姓名与邮箱将作为唯一的key以便获取回复信息，建议勾选保存在浏览器中'|
|nameText|姓名|String|'姓名'|
|emailText|邮箱|String|'邮箱'|
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
|likeColor|点赞的颜色，可以是16进制数，也可以是颜色单词|String|'red'|
|unlikeColor|踩的颜色，可以是16进制数，也可以是颜色单词|String|'gray'|

##### 方法 

所有方法除submit外均需要回调布尔值，回调true则继续执行下一步操作，不会使用回调的话，请看例子demo.vue,会简化到只包含调用的4个方法

submit函数需要执行回调cab({id:new_id,content:htmlEscape(form.content),name:form.name,created_at:created_at})

|方法名称|作用|参数|参数含义|
|clickReport|举报|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|clickLike|点赞|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|clickUnlike|踩|row，cab|row为通讯对象，cab为回调函数，成功则执行cab(true)|
|submit|提交评论|row，cab|row为通讯对象，cab为回调函数|


 
 




<h5 id='change'>修改依赖</h5>

##### 改组件依赖ant-design-vue 与 jquery，如需使用element或其他组件，改动不大，改动如下：

###### ant 需要修改的属性

1. a-col用于适配手机与pc评论列表页的响应式样式，修改span即可
2. 子组件中点赞与踩的icon，a-icon
3. 表单a-form

###### jquery 需要修改的属性

1.点击回复时的回复窗口定位以及滚动动画效果，位于index.vue中