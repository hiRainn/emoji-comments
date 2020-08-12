<template>
	<div class="comments-list">
		<a-row class="comments-list-item" v-for="(item,index) in comments" v-bind:key="index" :id="'floor_' + item.data.id">
			<a :href="'#replay_'+item.data.id"></a>
			<div class="comments-list-item-heading">
				<a-row>
					<a-col :span="2">
						<img src="../assets/img/heading.jpg" style="" />
					</a-col>
					<a-col :span="22">
						<a-row>
							<a-col :span="20">
								<span class="comments-list-item-username">{{item.data.name || AnonymousText}}</span>
								<span >  {{getReplayName(item.data.pid)}}</span>
							</a-col>
							<a-col :span="4">
								<span class="time">{{item.data.created_at || ''}}</span>
							</a-col>
						</a-row>
						<a-row>
							<div class="comments-list-item-content" v-html="item.data.content"></div>
						</a-row>
						<a-row class="comment-under">
							<span  class="replay_pc" v-if="showReplay && item.data.id">
								<span @click="Replay({item:item,index:index})">
									{{replayText}}
								</span>
							</span>
							
							<span class="replay_pc" :data="item.data.id"  v-if="showUnlike && item.data.id">
								<span @click="UnLike({item:item,index:index})"><a-icon :theme="item.data.unlike?'filled':'outlined'" :style="{color:unlikeColor}" type="dislike" /></span>
								{{getItemUnlikeNumber(item)}}
							</span>
							<span class="replay_pc" :data="item.data.id"  v-if="showLike && item.data.id">
								<span @click="Like({item:item,index:index})"><a-icon :theme="item.data.like?'filled':'outlined'" :style="{color:likeColor}" type="like" /> </span>
								{{getItemLikeNumber(item)}}
							</span>
	
							<span class="replay_pc" :data="item.data.id"  v-if="showReport && item.data.id">
								<span @click="Report({item:item,index:index})" :style="{color:item.data.report?likeColor:'black'}">
									{{reportText}}
								</span>
							</span>
						</a-row>
					</a-col>
				</a-row>
			</div>

			<a :id="'replay_'+item.data.id"></a>
			<div class="item-child" v-if="item.children.length > 0">
				<list 
				@Replay="Replay"
				@clickReport="clickReport" 
				@clickUnlike="clickUnlike" 
				@clickLike="clickLike" 
				:AnonymousText="AnonymousText"
				:replayText="replayText" 
				:reportText="reportText" 
				:showLike="showLike"
				:showUnlike="showUnlike"
				:showReplay="showReplay"
				:showReport="showReport"
				:likeColor="likeColor"
				:unlikeColor="unlikeColor"
				:comments="item.children" />
			</div>
		</a-row>
	</div>
</template>

<script>
	export default {
		name: 'List',
		props: {
			AnonymousText:{
				type: String,
				default: '匿名用户'
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
			likeColor:{
				type: String,
				default: 'red',//mixed
			},
			unlikeColor:{
				type: String,
				default: 'gray',//mixed
			},
			comments: {
				type: Array,
				default: []
			}
		},
		data() {
			return {
				replayName:[]
			}
		},
		methods: {
			emoji(word) {
				// 生成html
				const type = word.substring(1, word.length - 1);
				return `<span class="emoji-item-common emoji-${type} emoji-size-small" ></span>`;
			},
			Replay(row) {
				this.$emit('Replay',row )
			},
			Report(row) {
				if(typeof(row.item.data.report) != 'undefined') {
					if(row.item.data.report == 1) {
						return false
					}
				}
				this.$emit('clickReport',row.item.data, r=> {
					if(r) {
						row.item.data.report = 1
						this.$set(this.comments,row.index,row.item)
					} 
				})
			},
			clickReport(row,car) {
				this.$emit('clickReport',row ,r => {
					car(r)
				})
			},
			Like(row) {
				this.$emit('clickLike',row.item.data, r=> {
					if(r) {
						row.item.data.like_number++
						row.item.data.like = 1
						this.$set(this.comments,row.index,row.item)
					} 
				})
			},
			clickLike(row,car) {
				this.$emit('clickLike',row, r => {
					car(r)
				})
			},
			UnLike(row) {
				this.$emit('clickUnlike',row.item.data, r=> {
					if(r) {
						row.item.data.unlike_number++
						row.item.data.unlike = 1
						this.$set(this.comments,row.index,row.item)
					} 
				})
			},
			clickUnlike(row,car) {
				this.$emit('clickUnlike',row, r => {
					car(r)
				})
			},
			
		},
		computed:{
			getItemLikeNumber() {
				return function(item) {
					if(typeof(item.data.like_number) == 'undefined') {
						item.data.like_number = 0
					} else if (typeof(item.data.like_number) == 'number') {
						if(isNaN(item.data.like_number)) {
							item.data.like_number = 0
						}
					} else {
						let res = parseInt(item.data.like_number);
						if(isNaN(res)) {
							res = 0
						}
						item.data.like_number = res
					}
					return item.data.like_number
				}
			},
			getItemUnlikeNumber() {
				return function(item) {
					if(typeof(item.data.unlike_number) == 'undefined') {
						item.data.unlike_number = 0
					} else if (typeof(item.data.unlike_number) == 'number') {
						if(isNaN(item.data.unlike_number)) {
							item.data.unlike_number = 0
						}
					} else {
						let res = parseInt(item.data.unlike_number);
						if(isNaN(res)) {
							res = 0
						}
						item.data.unlike_number = res
					}
					return item.data.unlike_number
				}
			},
			getReplayName() {
				return function(pid) {
					if(pid === 0 || this.replayName[pid] == undefined) {
						return ''
					} else {
						return '@ ' + this.replayName[pid]
					}
				}
			},
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
			}
		},
	};
</script>
<style lang="scss">
	.emoji-item-common {
		background: url("../assets/img/emoji_sprite.png");
		display: inline-block;

		&:hover {
			cursor: pointer;
		}
	}
	
	.replay_pc{
		float: right;
		margin-left: 30px;
		
		span:hover{
			cursor: pointer;
		}
	}
	


	.time {
		display: inline-block;
		float: right;
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
		margin-left: 50px;
	}

	.comments-list-item {
		border-bottom: 1px dotted #eee;
	}
	
	
</style>
