<template>
	<div class="comments-list">
		<el-row class="comments-list-item" v-for="(item,index) in comments" v-bind:key="index" :id="'floor_' + item.data.id">
			<a :href="'#replay_'+item.data.id"></a>
			<div class="comments-list-item-heading">
				<el-row>
					<el-col :span="2">
						<img src="../assets/img/heading.jpg" style=""/>
					</el-col>
					
					<el-col :span="22">
						<el-row>
							<el-col :span="20">
								<span class="comments-list-item-username">{{item.data.name}} -- {{item.data.id}}</span>
							</el-col>
							<el-col :span="4">
								<span class="time">刚刚</span>
							</el-col>
						</el-row>
						<el-row>
							<div class="comments-list-item-content" v-html="item.data.content"></div>
						</el-row>
						<el-row>
							<span class="replay_pc" :data="item.data.id" style="float: right;margin-left: 40px;">{{replayText}}</span>
							<span class="replay_pc" :data="item.data.id" style="float: right;margin-left: 40px;">{{reportText}}</span>
							
							<span class="replay_pc" :data="item.data.id" style="float: right;margin-left: 40px;">{{like}}</span>
							<span class="replay_pc" :data="item.data.id" style="float: right;margin-left: 40px;">{{unlike}}</span>
						</el-row>
					</el-col>
				</el-row>
				
				
			
			</div>
			
			<a :id="'replay_'+item.data.id"></a>
			<div class="item-child" v-if="item.children.length > 0">
				<list :replayText="replayText" :reportText="reportText" :comments="item.children"></list>
			</div>
		</el-row>

	</div>
</template>
<script>
	export default {
		name: 'List',
		props: {
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
	.time{
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
	.item-child {
		margin-left: 50px;
		border-left: 1px dotted #eee;
		border-bottom: none !important
	}

	.comments-list-item {
		border-bottom: 1px dotted #eee;
	}
</style>
