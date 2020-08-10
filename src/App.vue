<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <comment
    	@submit="comment" 
    	:reportText="'举报'" 
    	:showName="true" 
    	:showEmail="true"
    	:replayText="'回复'" 
    	:list="getCommentList" 
    	:content="form.content" 
    	ref="comment" />
  </div>
</template>

<script>
import $ form 'jquery'
import Comment from './components'

export default {
  name: 'App',
  components: {
    Comment
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
