<template>
  <div>
	  <div class="item" v-for="item in itemList">
		  <el-row>
			  <span class="title">
					<router-link style="font-size: 22px;color: #303133;" :to="'/article/' + item.id" >{{item.title}}</router-link>
			  </span>
		  </el-row>
		  <el-row>
			  <span class="info">{{$t('article.publish')}} {{item.post_at}}</span>
			  <span class="info">
				  <router-link style="color: #2C3E50;" :to="'/article/cate_id/' + item.cate_id" >{{item.cate_name}}</router-link>
			  </span>
			  <i class="el-icon-view">{{item.views}}</i>
			  <i class="el-icon-chat-dot-square">{{item.comments}}</i>
		  </el-row>
		  <el-row class="content">
			  {{item.content}}
		  </el-row>
		  <el-row class="tag">
			  <el-tag type="info" class="tag" style="margin-right: 10px;" v-if="item.tag_name != false" v-for="(v,k) in item.tag_name.split(',')">
				  <router-link style="font-size: 12px;margin-right: 5px;color: #606266;" :to="'/article/tag/' + v" >{{v}}</router-link>
			  </el-tag>
		  </el-row>
		  <el-divider></el-divider>
	  </div>
	  
  </div>
</template>

<script>

export default {
  name: 'ArticleItem',
  props: {
    list: {
      type: Array,
      required: true
    },
  },
  computed: {
    itemList() {
      return this.list
    },
  }
}
</script>

<style scoped>
	.item{
		text-align: left;
	}
	a {
		text-decoration: none;
	}
	.title{
		font-size: 24px;
	}
	.router-link-active {
		text-decoration: none;
	}
	.info{
		color:#606266;
		margin-right: 15px;
		font-size: 14px;
	}
	i{
		margin-right: 10px;
		font-size: 14px;
	}
	.content{
		margin-top: 15px;
	}
	.tag{
		margin-top: 10px;
		margin-right:5px;
	}
	.el-divider--horizontal{
		margin:15px 0px
	}
</style>
