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
							</a-col>
							<a-col :span="4">
								<span class="time">刚刚</span>
							</a-col>
						</a-row>
						<a-row>
							<div class="comments-list-item-content" v-html="item.data.content"></div>
						</a-row>
						<a-row class="comment-under">
							<span  class="replay_pc" @click="clickReplay(item.data.id)" v-if="showReplay && item.data.id">
								<span>
									{{replayText}}
								</span>
							</span>
							
							<span class="replay_pc" :data="item.data.id"  v-if="showUnlike && item.data.id">
								<span @click="clickUnlike(item.data.id)"><a-icon :theme="'filled'" :style="{color:unlikeColor}" type="dislike" /></span>
								{{unlike}}
							</span>
							<span class="replay_pc" :data="item.data.id"  v-if="showLike && item.data.id">
								<span @click="clickLike(item.data.id)"><a-icon :theme="'filled'" :style="{color:likeColor}" type="like" /> </span>
								{{like}}
							</span>
	
							<span class="replay_pc" :data="item.data.id"  v-if="showReport && item.data.id">
								<span @click="clickReport(item.data.id)">
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
				@clickReplay="clickReplay"
				@clickReport="clickReport" 
				@clickUnlike="clickUnlike" 
				@clickLike="clickLike" 
				:AnonymousText="AnonymousText"
				:replayText="replayText" 
				:reportText="reportText" 
				:like="like"
				:unlike="unlike"
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
			like: {
				type: Number,
				default: 0
			},
			unlike: {
				type: Number,
				default: 0
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

			}
		},
		methods: {
			emoji(word) {
				// 生成html
				const type = word.substring(1, word.length - 1);
				return `<span class="emoji-item-common emoji-${type} emoji-size-small" ></span>`;
			},
			clickReplay(id) {
				this.$emit('clickReplay',id)
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
		updated() {
			for (let p in this.comments) {
				this.comments[p]['data']['name'] = this.comments[p]['data']['name']
				this.comments[p]['data']['content'] = this.comments[p]['data']['content'].replace(/:.*?:/g, this.emoji);
			}
		},
		mounted() {
			if (this.comments.length > 0) {
				for (let p in this.comments) {
					this.comments[p]['data']['name'] = this.comments[p]['data']['name']
					this.comments[p]['data']['content'] = this.comments[p]['data']['content'].replace(/:.*?:/g, this.emoji);
				}
			}
		}
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
