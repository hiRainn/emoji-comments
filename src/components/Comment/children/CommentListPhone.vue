<template>
	<div class="comments-list">
		<a-row class="comments-list-item" v-for="(item,index) in comments" v-bind:key="index" >
			<div class="comments-list-item-heading">
				<!-- <a-avatar :size="100" fit="contain" src="./assets/img/heading.jpg"></a-avatar> -->
				<img src="../assets/img/heading.jpg" style="width: 30px;height: 30px;"/>
				<span class="comments-list-item-username">{{item.data.name}} -- {{item.data.id}}</span>
			</div>
			<div class="comments-list-item-content" v-html="item.data.content"></div>
			<div class="item-child" v-if="item.children.length > 0">
				<list :comments="item.children"></list>
			</div>
		</a-row>
		<!-- <div class="comments-list-item" v-for="(item,index) in comments" v-bind:key="index">
			<div class="comments-list-item-heading">
				<img src="./assets/img/heading.jpg" />
				<span class="comments-list-item-username">{{item.data.name}} -- {{item.data.id}}</span>
			</div>
			<div class="comments-list-item-content" v-html="item.data.content"></div>
			<div class="item-child" v-if="item.children.length > 0">
				<list :comments="item.children"></list>
			</div>
		</div> -->
	</div>
</template>
<script>
	export default {
		name: 'List',
		props: {
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
	.item-child {
		margin-left: 30px;
		border-left: 1px dotted #eee;
		border-bottom: none !important
	}

	.comments-list-item {
		border-bottom: 1px dotted #eee;
	}
</style>
