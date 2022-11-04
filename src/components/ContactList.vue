<template>
	<div class="lists-header">
		<h4>Contact List</h4>
		<div class="search-bar">
			<input type="text" v-model="search" placeholder="Type to search...">
			<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16"><path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/></svg>
		</div>
	</div>
	<div class="lists">
		<div class="list" v-for="(user, i) in filteredList" :key="i" @click="viewMessage(user.uid)">
			<div class="avatar">
				<img :src="user.avatar">
			</div>
			<div class="content-list">
				<h3>{{user.name}}</h3>
			</div>
		</div>
	</div>
</template>
<script>
	import firebase from 'firebase/compat/app'
	import 'firebase/compat/auth'
	import 'firebase/compat/firestore'
	export default{
		name : 'ContactList',
		data(){
			return{
				user : firebase.auth().currentUser,
				db : firebase.firestore(),
				search : '',
				users : []
			}
		},
		mounted(){
			this.db.collection('users')
			.orderBy('name')
			.onSnapshot(querySnap => {
				this.users = []
				let usr = querySnap.docs.map(doc => doc.data())
				for(let i of usr){
					if(i.userID != this.user.uid){
						this.users.push({
							name : i.name,
							avatar : i.avatar,
							uid : i.userID
						})
					}
				}
			})
		},
		inheritAttrs : false,
		methods : {
			viewMessage(uid){
				this.$emit('view-message', uid)
			}
		},
		computed : {
			filteredList(){
				return this.users.filter(post => {
					return post.name.toLowerCase().includes(this.search.toLowerCase())
				})
			}
		}
	}
</script>
<style scoped>
	.lists-header{
		width: 100%;
		height: auto;
		padding: 10px;
		margin: 0;
		text-align: left;
		position: relative;
		display: flex;
		align-items: center;
		z-index: 100;
		margin-top: 10px;
	}
	.lists-header h4{
		font-size: 30px;
		padding: 0 10px;
	}
	.lists-header .search-bar{
		background: transparent;
		width: auto;
		height: auto;
		display: flex;
		align-items: center;
		position: absolute;
		right: 10px;
		padding: 6px;
	}
	.lists-header .search-bar input{
		width: 230px;
		height: 40px;
		padding: 15px 30px 15px 15px;
		outline: none;
		border: none;
		background: transparent;
		box-shadow: 0 0 3px 0 #4e4e4e;
		border-radius: 20px;
		font-size: 17px;
	}
	.lists-header .search-bar svg{
		position: absolute;
		right: 15px;
	}
	.lists{
		width: 100%;
		height: auto;
		padding: 10px;
		margin: 0;
		text-align: left;
		overflow-y: auto;
	}
	.lists h4{
		padding: 10px;
		font-size: 30px;
	}
	.lists .list{
		width: 100%;
		height: auto;
		display: flex;
		align-items: center;
		transition: 0.50s;
		padding: 10px;
		cursor: pointer;
		border-radius: 10px;
	}
	.lists .list:hover{
		background: rgba(80, 80, 80, 0.30);
	}
	.lists .list .avatar{
		width: 40px;
		height: 40px;
		background: forestgreen;
		border-radius: 50%;
		overflow-y: hidden;
	}
	.lists .list .avatar img{
		width: 100%;
		height: 100%;
	}
	.lists .list .content-list{
		margin-left: 10px;
		width: auto;
		height: auto;
		text-align: left;
	}
	.lists .list .content-list h3{
		padding: 6px;
	}
	.lists .list .content-list p{
		padding-left: 6px;
	}
	@media(max-width: 576px){
		.lists-header h4{
			font-size: 22px;
		}
		.lists-header .search-bar input{
			font-size: 16px;
			width: 180px;
			height: 30px;
			padding: 8px 20px 8px 10px;
		}
	}
</style>