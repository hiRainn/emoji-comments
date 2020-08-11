<template>
	<div class="comment-wrap">
		<a-row class="row-comment-list">
			<comment-list 
			@clickReplay="clickReplay"  
			@clickReport="clickReport" 
			@clickUnlike="clickUnlike" 
			@clickLike="clickLike" 
			:comments="getCommentList" 
			:showLike="showLike"
			:showUnlike="showUnlike"
			:showReplay="showReplay"
			:showReport="showReport"
			:showEmail="showEmail"
			:showName="showName"
			:AnonymousText="AnonymousText"
			:like="like"
			:unlike="unlike"
			:likeColor="likeColor"
			:unlikeColor="unlikeColor"
			:replayText="replayText" 
			:reportText="reportText" />
			<comment-list-phone :comments="getCommentList" class="hidden-sm-and-up"></comment-list-phone>
		</a-row>
		
		<a-row class="comment-area">
			<a href="#replay" id="areplay"></a>
			<a-row class="comment" id="replay">
				<span :hidden="!showTip">{{tipText}}</span>
				<a-form :label-position="label_position" label-width="80px">
					<a-form-item v-if="showName" :label="nameText">
						<a-input v-model="form.name" @change="changesave"></a-input>
					</a-form-item>
					<a-form-item v-if="showEmail" :label="emailText">
						
						<a-input v-model="form.email" @change="changesave"></a-input>
					</a-form-item>
			
					<a-form-item>
						<a-row>
							<a-input type="textarea" id="textpanel" rows="3" v-model="form.content"></a-input>
							<!-- <a-input type="textarea" class="comment-input" placeholder="" id="textpanel" v-model="content"></a-input> -->
							<div class="opration">
								<div class="emoji-panel-btn" @click="showEmojiPanel">
									<img src="./assets/img/face_logo.png" />
								</div>
								<div>
									<a-button @click="cancle" round size="small" style="align-self: flex-end;" v-if="form.pid != 0" :hidden="form.pid ">{{cancleText}}</a-button>
									<a-button @click="saveComment" round size="small" style="align-self: flex-end;" :disabled="form.content.trim() == ''">{{buttonText}}</a-button>
								</div>
								
							</div>
						</a-row>
						
						<a-row>
							<emoji-panel @emojiClick="appendEmoji" v-if="isShowEmojiPanel"></emoji-panel>
						</a-row>
					</a-form-item>
					<a-form-item>
						<a-checkbox v-model="save" @change="setLocal">{{saveText}}</a-checkbox>
					</a-form-item>
				</a-form>
			
			</a-row>
			
		</a-row>
		
	</div>
</template>
<script>
	import $ from 'jquery'
	import EmojiPanel from "./children/EmojiPanel.vue";
	import CommentList from "./children/CommentList.vue";
	import CommentListPhone from "./children/CommentListPhone.vue";
	export default {
		props: {
			list:{
				type:Array,
				default:[]
			},
			AnonymousText:{
				type: String,
				default: '匿名用户'
			},
			tipText:{
				type: String,
				default: '你的邮箱不会显示，姓名与邮箱将作为唯一的key以便获取回复信息，建议勾选保存在浏览器中'
			},
			nameText:{
				type: String,
				default: '姓名'
			},
			emailText:{
				type: String,
				default: '邮箱'
			},
			buttonText: {
				type: String,
				default: '提交'
			},
			cancleText: {
				type: String,
				default: '取消'
			},
			saveText:{
				type: String,
				default: '保存在浏览器中'
			},
			content: {
				type: String,
				default: ''
			},
			reportText:{
				type:String,
				default:'report'
			},
			replayText:{
				type:String,
				default:'replay'
			},
			like:{
				type:Number,
				default:0
			},
			unlike:{
				type:Number,
				default:0
			},
			showReport:{
				type:Boolean,
				default:true
			},
			showReplay:{
				type:Boolean,
				default:true
			},
			showLike:{
				type:Boolean,
				default:true
			},
			showUnlike:{
				type:Boolean,
				default:true
			},
			showName:{
				type:Boolean,
				default:false
			},
			showEmail:{
				type:Boolean,
				default:false
			},
			showTip:{
				type:Boolean,
				default:false
			},
			likeColor:{
				type: String,
				default: 'red',//mixed
			},
			unlikeColor:{
				type: String,
				default: 'gray',//mixed
			},
		},
		data() {
			return {
				save:false,
				label_position: 'top',
				form:{
					pid:0,
					name:'',
					email:'',
					content:this.content
				},
				isShowEmojiPanel: false,
			}
		},
		computed:{
			getCommentList(){
				return this.list
			},
			getReplayText(){
				return this.replayText
			}
		},
		components: {
			EmojiPanel,
			CommentList,
			CommentListPhone
		},
		methods: {
			
			cancle() {
				this.form.pid = 0;
				$('#replay').insertAfter($('#areplay'))
			},
			saveComment() {
				// this.comments.push(this.content.replace(/:.*?:/g, this.emoji)); // 替换":"符号包含的字符串,通过emoji方法生成表情<span></span>
				this.isShowEmojiPanel = false;
				var data = {
					pid:parseInt(this.form.pid),
					content : this.form.content
				}
				if(this.showName ) {
					data.name = this.form.name
				}
				if(this.showEmail) {
					this.form.email = this.form.email
				}
				this.$emit('submit', this.form)
			},
			showEmojiPanel() {
				this.isShowEmojiPanel = !this.isShowEmojiPanel;
			},
			appendEmoji(text) {
				const el = document.getElementById("textpanel");
				this.isShowEmojiPanel = false;
				this.form.content = el.value + ":" + text + ":";
			},
			cleanContent() {
				this.form.content = ''
			},
			setLocal(val) {
				if(val) {
					localStorage.setItem('comment_name',this.form.name)
					localStorage.setItem('comment_email',this.form.email)
					localStorage.setItem('comment_save',1)
				} else {
					localStorage.removeItem('comment_name')
					localStorage.removeItem('comment_email')
					localStorage.removeItem('comment_save')
				}
			},
			changesave() {
				if(this.save) {
					localStorage.setItem('comment_name',this.form.name)
					localStorage.setItem('comment_email',this.form.email)
				}
			},
			clickReplay(id) {
				this.form.pid = parseInt(id)
				var id = '#replay_' + this.form.pid
				$('#replay').appendTo($(id))
				$("html,body").animate({scrollTop: $("#replay").offset().top - "100" + "px"}, 400);
			},
			clickReport(id) {
				this.$emit('clickReport',id)
			},
			clickUnlike(id) {
				this.$emit('clickUnlike',id)
			},
			clickLike(id) {
				this.$emit('clickLike',id)
			},
		},
		mounted(){
			//get comment info
			var save = localStorage.getItem('comment_save')
			if(save) {
				this.save = true
				this.form.name = localStorage.getItem('comment_name')
				this.form.email = localStorage.getItem('comment_email')
			}
		}
	};
</script>
<style lang="scss">
	// 注意 这里因为v-html的原因 不能使用scoped 不然样式不能失效
	.row-comment-list{
		text-align: left;
	}
	
	.comment-area {
		text-align: left;
	}
	.comment-wrap {
		.emoji-item-common {
			background: url("./assets/img/emoji_sprite.png");
			display: inline-block;

			&:hover {
				cursor: pointer;
			}
		}

		.emoji-size-small {
			// 表情大小
			zoom: 0.4;
		}

		.emoji-size-large {
			zoom: 0.4; // emojipanel表情大小
			margin: 2px;
		}

		.comments-list {
			margin-top: 20px;

			.comments-list-item {
				margin-bottom: 20px;

				.comments-list-item-heading {
					img {
						height: 50px;
						width: 50px;
						margin-right: 20px;
						border-radius: 50%;
						vertical-align: text-top;
					}

					.comments-list-item-username {
						margin-left: 5px;
						font-weight: bold;
					}
				}

				.comments-list-item-content {
					margin: 10px 0px;

					&:last-child {
						border-bottom: 0;
					}

					span {
						vertical-align: top;
					}
				}
			}
		}

		.comment-input {
			height: 100px;
			width: 500px;
			border: 1px solid #cccccc;
			border-radius: 5px;
			padding: 10px;
			resize: none;

			&:focus {
				outline: none;
			}
		}

		.opration {
			display: flex;
			justify-content: space-between;
			position: relative;

			.emoji-panel-btn {
				display: inline-block;

				&:hover {
					cursor: pointer;
					opacity: 0.8;
				}

				img {
					height: 24px;
					width: 24px;
				}
			}
		}
	}

	@import "./assets/css/emoji.css"; // 导入精灵图样式
</style>
