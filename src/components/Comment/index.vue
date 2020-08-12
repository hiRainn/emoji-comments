<template>
	<div class="comment-wrap">
		<a-row class="row-comment-list">
			<a-col  :xs="0" :md="24">
				<!-- v-if for async  -->
				<comment-list
				v-if="list.length > 0" 
				@Replay="Replay"  
				@clickReport="clickReport" 
				@clickUnlike="clickUnlike" 
				@clickLike="clickLike" 
				:comments="list" 
				:showLike="showLike"
				:showUnlike="showUnlike"
				:showReplay="showReplay && allowComment"
				:showReport="showReport"
				:showEmail="showEmail"
				:showName="showName"
				:AnonymousText="AnonymousText"
				:likeColor="likeColor"
				:unlikeColor="unlikeColor"
				:replayText="replayText" 
				:reportText="reportText" />
			</a-col>
			<a-col  :xs="24" :md="0">
				<!-- <comment-list-phone :comments="list" class="hidden-sm-and-up"></comment-list-phone> -->
			</a-col>
		</a-row>
		
		<a-row class="comment-area" v-if="allowComment">
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
									<a-button @click="cancle" round size="small" style="align-self: flex-end;margin-right: 10px;" v-if="form.pid != 0" :hidden="!form.pid ">{{cancleText}}</a-button>
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
			allowComment:{
				type:Boolean,
				default:true
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
				this.$emit('submit', this.form , r => {
					//update list   r =  { id:, content:, name:,   created_at:，pid:, }
					if(r) {
						var data = {
							data :{id:r.id, name:r.name, content:r.content, pid:this.form.pid, created_at:r.created_at},
							children:[]
						}
						if(this.form.pid == 0) {
							//comment article direct
							let length = this.list.length 
							this.$set(this.list,length,data)
						} else {
							for(let p in this.list) {
								let length = this.list[p]['children'].length
								//replay the comment of article
								if(this.list[p]['data']['id'] == this.form.pid) {
									this.$set(this.list[p]['children'],length,data)
									break;
								}
								//replay the comment of comment
								if(this.list[p]['children'].length > 0) {
									for (let q in this.list[p]['children']) {
										if(this.list[p]['children'][q]['data']['id'] == this.form.pid) {
											this.$set(this.list[p]['children'],length,data)
											break
										}
									}
								}
							}
							this.cancle()
							
						}
						this.cleanContent()
					}
				})
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
			Replay(row) {
				var id = row.item.data.id
				this.form.pid = parseInt(id)
				var id = '#replay_' + this.form.pid
				$('#replay').appendTo($(id))
				$("html,body").animate({scrollTop: $("#replay").offset().top - "130" + "px"}, 400);
				$('#textpanel').focus()
			},
			clickReport(row,car) {
				this.$emit('clickReport',row, r => {
					car(r)
				})
			},
			clickUnlike(row,car) {
				this.$emit('clickUnlike',row, r => {
					car(r)
				})
			},
			clickLike(row,car) {
				this.$emit('clickLike',row, r => {
					car(r)
				})
			},
		},
		updated(){
			
		},
		created(){
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
