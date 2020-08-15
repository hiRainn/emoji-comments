<template>
	<div>
		<a-row>
			<comment 
				@submit="comment"
				@clickUnlike="clickUnlike" 
				@clickLike="clickLike" 
				@clickReport="clickReport"
				@cancleReport="cancleReport"
				@cancleLike="cancleLike"
				@cancleUnlike="cancleUnlike"
				repeatType="cancle"
				:allowComment="Boolean(article.allow_comment)"
				:showReport="true"
				:reportText="'举报'" 
				:showName="true" 
				:showEmail="true"
				:replayText="'回复'" 
				:list="comment_list" 
				title="total {Number} comments"
				ShowText="click to view all {Number} comments"
				HideText="hide comment"
				:content="form.content" 
				ref="comment" />
		</a-row>
	</div>
</template>

<script>
	import {
		getComments,
		commentArticle
	} from '@/api/article.js'
	import {
		mavonEditor
	} from 'mavon-editor'

	import Comment from '@/components/Comment'
	export default {
		components: {
			Comment,
		},
		data() {
			return {
				comment_number:0,
				comment_list: [],
				form: {
					pid:0,
					name: '',
					email: '',
					content: '',
					article_id: '',
				}
			}
		},
		methods: {
			//get comment
			getComments(id) {
				getComments(id).then(r => {
					if (r.code) {
						this.$alert(r.msg)
						return;
					} else {
						this.comment_list = r.data.list
						this.comment_number = r.data.total
					}
				}).catch(e => {
					console.log(e)
				})
			},
			//post comment
			comment(value,car) {
				this.form.pid = value.pid
				this.form.name = value.name
				this.form.email = value.email
				this.form.content = value.content
				this.form.article_id = parseInt(this.$route.params.id)
				commentArticle(this.form).then(r => {
					this.form.submit = false;
					if (r.code) {
						console.log(r)
						return;
					}
					this.$message.success(this.$i18n.t('comment.success'));
					var data = {
						id:r.data.id,
						name: this.form.name,
						content: this.htmlEscape(this.form.content),
						pid:form.pid,
						created_at:r.created_at
					}
					if(r.data.status == 0) {
						this.comment_number++
					}
					this.form.content = ''
					car(data)

				}).catch(e => {
					console.log(e)
				})
			},
			
			htmlEscape(text){
			  return text.replace(/[<>"&]/g, function(match, pos, originalText){
			    switch(match){
			    case "<": return "&lt;";
			    case ">":return "&gt;";
			    case "&":return "&amp;";
			    case "\"":return "&quot;";
			  }
			  });
			},
			clickUnlike(row,car) {
				// item.data.unlike_number++
				car(true)
			},
			clickLike(row,car) {
				car(true)
			},
			clickReport(row,car) {
				car(true)
			},
			cancleUnlike(row,car) {
				// item.data.unlike_number++
				car(true)
			},
			cancleLike(row,car) {
				car(true)
			},
			cancleReport(row,car) {
				car(true)
			}
		},
		mounted() {
			var id = this.$route.params.id
			this.getContent(id)
			this.getComments(id)
		
		}
	}
</script>

