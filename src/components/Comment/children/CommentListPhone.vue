<template>
	<div class="comments-list">
		<a-row class="comments-list-item" :hidden="hideNumber -1  < index && !showSubComments && isChildren && hideNumber > 0" v-for="(item,index) in comments" v-bind:key="index" :id="'floor_' + item.data.id">
			<div :hidden="hideNumber -1  < index && !showSubComments && isChildren && hideNumber > 0" class="comments-list-item-heading-phone" :id= "PhoneAnchor+ item.data.id">
				<a-row>
					<a-col :span="2" :class="{'phone-img':isChildren}">
						<img  v-if="item.data.is_admin" src="../assets/img/author.png" style="" />
						<img  v-else src="../assets/img/heading.jpg" style="" />
					</a-col>
					<a-col :span="22">
						<a-row>
							<span class="comments-list-item-username">{{item.data.name || AnonymousText}}</span>
							<span :style="{color:AdminTagColor}" v-if="item.data.is_admin">&nbsp;({{AdminText}})</span>
							<span style="margin: 0px 5px;color: #1890FF;" v-if="getReplayName(item.data.pid) != ''">{{replayText}}</span>
							<span> {{getReplayName(item.data.pid)}}</span>
							<span v-if="getAdminTag(item.data.pid)" :style="{color:AdminTagColor}">&nbsp;({{AdminText}})</span>
						</a-row>
						<a-row>
							<div class="comments-list-item-content-phone" v-html="item.data.content"></div>
						</a-row>
						<a-row class="comment-under">
							
							<span class="time">{{item.data.created_at || ''}}</span>
							
							<span class="replay_pc" v-if="showReplay && item.data.id">
								<span @click="PhoneReplay({item:item,index:index,floor_id:floorId})">
									{{replayText}}
								</span>
							</span>

							<span class="replay_pc" :data="item.data.id" v-if="showUnlike && item.data.id">
								<span @click="UnLike({item:item,index:index})">
									<a-icon :theme="item.data.unlike?'filled':'outlined'" :style="{color:unlikeColor}" type="dislike" /></span>
								<span :id="'phone_unlike_'+ item.data.id">{{getItemUnlikeNumber(item)}}</span>
							</span>
							<span class="replay_pc" :data="item.data.id" v-if="showLike && item.data.id">
								<span @click="Like({item:item,index:index})">
									<a-icon :theme="item.data.like?'filled':'outlined'" :style="{color:likeColor}" type="like" /> </span>
								<span :id="'phone_like_'+ item.data.id">{{getItemLikeNumber(item)}}</span>
							</span>

							<span class="replay_pc" :data="item.data.id" v-if="showReport && item.data.id">
								<span @click="Report({item:item,index:index})" :style="{color:item.data.report?likeColor:'black'}">
									{{reportText}}
								</span>
							</span>
						</a-row>
					</a-col>
				</a-row>
			</div>

			<div class="item-child" v-if="item.children.length > 0">
				<list :ref="'subcomment_'+item.data.id" :floorId="item.data.id" @PhoneReplay="PhoneReplay" @cancleLike="cancleLike" @cancleUnlike="cancleUnlike" @clickReport="clickReport" :isChildren="true"
				 @cancleReport="cancleReport" @clickUnlike="clickUnlike" @clickLike="clickLike" :AdminText="AdminText"
				 :AdminTagColor="AdminTagColor" :AnonymousText="AnonymousText" :replayText="replayText" :reportText="reportText"
				 :showLike="showLike" :showUnlike="showUnlike" :showReplay="showReplay" :showReport="showReport" :likeColor="likeColor"
				 :unlikeColor="unlikeColor" :HideText="HideText" :ShowText="ShowText" :PhoneAnchor="PhoneAnchor" :repeatType="repeatType" :AnimateOn="AnimateOn" :hideNumber="hideNumber" :comments="item.children" />
			</div>
			<!-- click wo show all comments -->
			<a-row v-if="comments.length> hideNumber && !showSubComments && index == hideNumber - 1 && isChildren && hideNumber > 0">
				<span @click="showComments">{{getShowText(comments.length)}}</span>
			</a-row>
			<a-row v-if="comments.length> hideNumber && showSubComments && index == comments.length - 1 && isChildren && hideNumber > 0">
				<span @click="hideComments">{{HideText}}</span>
			</a-row>
		</a-row>
		
	</div>
</template>

<script>
	import $ from 'jquery'
	export default {
		name: 'List',
		props: {
			//determine whether is children component
			isChildren: {
				type: Boolean,
				default: false
			},
			AdminText: {
				type: String,
				default: 'author'
			},
			AnonymousText: {
				type: String,
				default: 'Anonymous'
			},
			reportText: {
				type: String,
				default: 'report'
			},
			replayText: {
				type: String,
				default: 'replay'
			},
			showReport: {
				type: Boolean,
				default: true
			},
			showReplay: {
				type: Boolean,
				default: true
			},
			showLike: {
				type: Boolean,
				default: true
			},
			showUnlike: {
				type: Boolean,
				default: true
			},
			hideNumber:{
				type: Number,
				default: 3, //mixed
			},
			likeColor: {
				type: String,
				default: 'red', //mixed
			},
			unlikeColor: {
				type: String,
				default: 'gray', //mixed
			},
			AdminTagColor: {
				type: String,
				default: '#8CC5FF', //mixed
			},
			AnimateOn: {
				type: Boolean,
				default: true
			},
			repeatType: {
				type: String,
				default: 'prevent' //prevent and cancle
			},
			ShowText:{
				type:String,
				default:'click to view all {Number} comments'
			},
			HideText:{
				type:String,
				default:'点击收起回复'
			},
			comments: {
				type: Array,
				default: []
			},
			PhoneAnchor:{
				type:String,
				default:'PhoneAnchor'
			},
			//to change refs showSubComments
			floorId:{
				type:Number,
				default:0
			}
		},
		data() {
			return {
				showSubComments:false,
				replayName: [],
				adminPid: [],
			}
		},
		methods: {
			showSubReplay(id) {
				var key = 'subcomment_' + id
				this.$refs[key][0].showComments()
			},
			showComments(){
				this.showSubComments = true
			},
			hideComments(){
				this.showSubComments = false
			},
			emoji(word) {
				// 生成html
				const type = word.substring(1, word.length - 1);
				return `<span class="emoji-item-common emoji-${type} emoji-size-small" ></span>`;
			},
			PhoneReplay(row) {
				this.$emit('PhoneReplay', row)
			},
			Report(row) {
				if (typeof(row.item.data.report) == 'undefined') {
					row.item.data.report = 0
				}
				if (row.item.data.report == 0) {
					this.$emit('clickReport', row.item.data, r => {
						if (r) {
							row.item.data.report = 1
							this.$set(this.comments, row.index, row.item)
						}
					})
				} else {
					if (this.repeatType == 'cancle') {
						this.$emit('cancleReport', row.item.data, r => {
							if (r) {
								row.item.data.report = 0
								this.$set(this.comments, row.index, row.item)
							}
						})
					} else {
						//默认阻止
						return false
					}
				}
			},
			clickReport(row, car) {
				this.$emit('clickReport', row, r => {
					car(r)
				})
			},
			cancleReport(row, car) {
				this.$emit('cancleReport', row, r => {
					car(r)
				})
			},
			Like(row) {
				if (typeof(row.item.data.like) == 'undefined') {
					row.item.data.like = 0
				}
				if (row.item.data.like == 0) {
					this.$emit('clickLike', row.item.data, r => {
						var id = row.item.data.id
						var color = this.likeColor
						if (r) {
							if (this.AnimateOn) {
								$.tipsBox({
									obj: $('#phone_like_' + id),
									str: "+1",
									color: color,
								});
							}
							row.item.data.like_number++
							row.item.data.like = 1
							this.$set(this.comments, row.index, row.item)
						}
					})
				} else {
					if (this.repeatType == 'cancle') {
						this.$emit('cancleLike', row.item.data, r => {
							if (r) {
								row.item.data.like = 0
								row.item.data.like_number--
								this.$set(this.comments, row.index, row.item)
							}
						})
					} else {
						//默认阻止
						return false
					}
				}

			},
			clickLike(row, car) {
				this.$emit('clickLike', row, r => {
					car(r)
				})
			},
			cancleLike(row, car) {
				this.$emit('cancleLike', row, r => {
					car(r)
				})
			},
			UnLike(row) {
				if (typeof(row.item.data.unlike) == 'undefined') {
					row.item.data.unlike = 0
				}
				if (row.item.data.unlike == 0) {
					this.$emit('clickUnlike', row.item.data, r => {
						var id = row.item.data.id
						var color = this.unlikeColor
						if (r) {
							if (this.AnimateOn) {
								$.tipsBox({
									obj: $('#phone_unlike_' + id),
									str: "+1",
									color: color,
								});
							}
							row.item.data.unlike_number++
							row.item.data.unlike = 1
							this.$set(this.comments, row.index, row.item)
						}
					})
				} else {
					if (this.repeatType == 'cancle') {
						this.$emit('cancleUnlike', row.item.data, r => {
							if (r) {
								row.item.data.unlike = 0
								row.item.data.unlike_number--
								this.$set(this.comments, row.index, row.item)
							}
						})
					} else {
						//默认阻止
						return false
					}
				}
			},
			clickUnlike(row, car) {
				this.$emit('clickUnlike', row, r => {
					car(r)
				})
			},
			cancleUnlike(row, car) {
				this.$emit('cancleUnlike', row, r => {
					car(r)
				})
			},

		},
		computed: {
			getItemLikeNumber() {
				return function(item) {
					if (typeof(item.data.like_number) == 'undefined') {
						item.data.like_number = 0
					} else if (typeof(item.data.like_number) == 'number') {
						if (isNaN(item.data.like_number)) {
							item.data.like_number = 0
						}
					} else {
						let res = parseInt(item.data.like_number);
						if (isNaN(res)) {
							res = 0
						}
						item.data.like_number = res
					}
					return item.data.like_number
				}
			},
			getItemUnlikeNumber() {
				return function(item) {
					if (typeof(item.data.unlike_number) == 'undefined') {
						item.data.unlike_number = 0
					} else if (typeof(item.data.unlike_number) == 'number') {
						if (isNaN(item.data.unlike_number)) {
							item.data.unlike_number = 0
						}
					} else {
						let res = parseInt(item.data.unlike_number);
						if (isNaN(res)) {
							res = 0
						}
						item.data.unlike_number = res
					}
					return item.data.unlike_number
				}
			},
			getReplayName() {
				return function(pid) {
					if (pid === 0 || this.replayName[pid] == undefined) {
						return ''
					} else {
						return this.replayName[pid]
					}
				}
			},
			getAdminTag() {
				return function(pid) {
					for (let p in this.adminPid) {
						if (this.adminPid[p] == pid) {
							return true
						}
					}
					return false
				}
			},
			getShowText() {
				return function(length) {
					return this.ShowText.replace('{Number}',length)
				}
			}
		},
		//for update new data
		updated() {
			//loop twice for any kind of sort
			for (let p in this.comments) {
				//this for updated function 
				this.comments[p]['data']['name'] = this.comments[p]['data']['name']
				this.comments[p]['data']['content'] = this.comments[p]['data']['content'].replace(/:.*?:/g, this.emoji);
				this.replayName[this.comments[p]['data']['id']] = this.comments[p]['data']['name']
			}
		},
		created() {
			//loop twice for any kind of sort 
			for (let p in this.comments) {
				//this for updated function 
				this.comments[p]['data']['name'] = this.comments[p]['data']['name']
				this.comments[p]['data']['content'] = this.comments[p]['data']['content'].replace(/:.*?:/g, this.emoji);
				this.replayName[this.comments[p]['data']['id']] = this.comments[p]['data']['name']
				if (this.comments[p]['data']['is_admin']) {
					this.adminPid.push(this.comments[p]['data']['id'])
				}
			}
			
			this.$nextTick(() => {
				let anchor = this.$route.hash.replace(/^#{1}/,'')
				if(anchor) {
					let id = parseInt(anchor.replace(this.PhoneAnchor,''))
					for(let p in this.comments) {
						if( this.hideNumber > 0 && p >= this.hideNumber && id == this.comments[p]['data']['id']) {
							this.showSubComments = true
							break
						}
					}
					this.$nextTick( () => {
						$("html,body").animate({scrollTop: $("#"+anchor).offset().top - 100}, 300);
					})
					
				}
			})

		},
	};

	(function($) {
		$.extend({
			tipsBox: function(options) {
				options = $.extend({
					obj: null, //jq对象，要在那个html标签上显示
					str: "+1", //字符串，要显示的内容;也可以传一段html，如: "<b style='font-family:Microsoft YaHei;'>+1</b>"
					startSize: "12px", //动画开始的文字大小
					endSize: "30px", //动画结束的文字大小
					interval: 600, //动画时间间隔
					color: "#cd4450", //文字颜色
					weight: "bold", //文字
					callback: function() {} //回调函数
				}, options);
				$("body").append("<span class='num'>" + options.str + "</span>");
				var box = $(".num");
				var left = options.obj.offset().left + options.obj.width() / 2;
				var top = options.obj.offset().top - options.obj.height();
				box.css({
					"position": "absolute",
					"left": left + "px",
					"top": top + "px",
					"z-index": 99999,
					"font-size": options.startSize,
					"line-height": options.endSize,
					"color": options.color,
					"font-weight": options.weight
				});
				box.animate({
					"font-size": options.endSize,
					"opacity": "0",
					"top": top - parseInt(options.endSize) + "px"
				}, options.interval, function() {
					box.remove();
					options.callback();
				});
			}
		});
	})($);
</script>
<style lang="scss">
	.emoji-item-common {
		background: url("../assets/img/emoji_sprite.png");
		display: inline-block;

		&:hover {
			cursor: pointer;
		}
	}

	.replay_pc {
		float: right;
		margin-left: 15px;
		font-size: 12px;

		span:hover {
			cursor: pointer;
		}
	}



	.time {
		font-size: 12px;
		display: inline-block;
		float: left;
	}

	.emoji-size-small {
		// 表情大小
		zoom: 0.4;
	}

	.emoji-size-large {
		zoom: 0.4; // emojipanel表情大小
		margin: 2px;
	}

	.comment-under {
		border-bottom: none !important
	}

	.item-child {
		margin-left: 30px;
		
		div.comments-list-item:last-child{
			margin-bottom: 0px;
			border-bottom: none;
		}
		
		div.comments-list-item:first-child{
			margin-top: 0px;
			padding-top: 10px;
			border-top: 1px dotted #eee;
		}
		
	}
	.phone-img img{
		height: 16px;
		width: 16px;
		margin-right: 15px;
		border-radius: 50%;
		vertical-align: text-top;
	}
	.comments-list-item {
		border-bottom: 1px dotted #eee;
	}
</style>
