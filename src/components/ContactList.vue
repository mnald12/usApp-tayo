<template>
	<div class="lists-header">
		<h4>Contact List</h4>
		<div class="search-bar">
			<input type="text" v-model="search" placeholder="Type to search...">
			<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-search" viewBox="0 0 16 16"><path d="M11.742 10.344a6.5 6.5 0 1 0-1.397 1.398h-.001c.03.04.062.078.098.115l3.85 3.85a1 1 0 0 0 1.415-1.414l-3.85-3.85a1.007 1.007 0 0 0-.115-.1zM12 6.5a5.5 5.5 0 1 1-11 0 5.5 5.5 0 0 1 11 0z"/></svg>
		</div>
		<div class="notif" @click="viewNotif()">
			<svg v-if="isOpen" xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-bell-slash-fill" viewBox="0 0 16 16"><path d="M5.164 14H15c-1.5-1-2-5.902-2-7 0-.264-.02-.523-.06-.776L5.164 14zm6.288-10.617A4.988 4.988 0 0 0 8.995 2.1a1 1 0 1 0-1.99 0A5.002 5.002 0 0 0 3 7c0 .898-.335 4.342-1.278 6.113l9.73-9.73zM10 15a2 2 0 1 1-4 0h4zm-9.375.625a.53.53 0 0 0 .75.75l14.75-14.75a.53.53 0 0 0-.75-.75L.625 15.625z"/></svg>
			<svg v-else xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-bell-fill" viewBox="0 0 16 16"><path d="M8 16a2 2 0 0 0 2-2H6a2 2 0 0 0 2 2zm.995-14.901a1 1 0 1 0-1.99 0A5.002 5.002 0 0 0 3 6c0 1.098-.5 6-2 7h14c-1.5-1-2-5.902-2-7 0-2.42-1.72-4.44-4.005-4.901z"/></svg>
		</div>
	</div>
	<div class="flex-container">
		<div class="lists">
			<div class="list" v-for="(user, i) in filteredList" :key="i" @click="viewMessage(user.uid)">
				<div class="avatar">
					<img :src="user.avatar">
				</div>
				<div class="content-list">
					<h3>{{user.name}}</h3>
					<p><span class="status" :class="setStatus(user.status)"></span>{{user.status}}</p>
				</div>
			</div>
		</div>
		<div v-if="isOpen" class="notif-displayer">
			<h3>Notification</h3>
			<hr>
			<div class="notification" v-for="(notif, i) in notifs" :key="i">
				<div class="notif-avatar">
					<img :src="notif.avatar">
				</div>
				<div class="notif-text">
					<h4>{{notif.name}} Message you</h4>
					<p>{{notif.msg}}</p>
				</div>
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
				users : [],
				isOpen : false,
				notifs : []
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
							uid : i.userID,
							status : i.status
						})
						this.db.collection('messages')
						.onSnapshot(querySnaps => {
							let mess = querySnaps.docs.map(doc => doc.data())
							for(let m of mess){
								if(m.to == this.user.uid && m.from == i.userID){
									console.log(1)
									let msgs = {
										msg : m.message,
										date : m.sendAt.toDate().toLocaleDateString("en-US"),
										avatar : i.avatar,
										name : i.name
									}
									this.notifs.push(msgs)
								}
							}
						})
					}
				}
			})

		},
		inheritAttrs : false,
		methods : {
			viewMessage(uid){
				this.$emit('view-message', uid)
			},
			setStatus(status){
				if(status == 'online') return 'online'
				else return 'offline'
			},
			viewNotif(){
				if(this.isOpen == false) this.isOpen = true
				else this.isOpen = false
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
		right: 50px;
		padding: 6px;
	}
	.lists-header .notif{
		background: transparent;
		width: auto;
		height: auto;
		display: flex;
		align-items: center;
		position: absolute;
		right: 10px;
		padding: 8px;
		box-shadow: 0 0 3px 0 black;
		border-radius: 50%;
		cursor: pointer;
		transition: 0.50s;
	}
	.lists-header .notif:hover{
		background: #2e2e2e;
		color: ghostwhite;
	}
	.lists-header .search-bar input{
		width: 300px;
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
	.flex-container{
		width: 100%;
		height: auto;
		position: relative;
		padding: 0;
		margin: 0;
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
		position: relative;
		display: flex;
		align-items: center;
		gap: 6px;
	}
	.lists .list .content-list p .status{
		width: 12px;
		height: 12px;
		border-radius: 50%;
	}
	.lists .list .content-list p .status.online{
		background: green;
	}
	.lists .list .content-list p .status.offline{
		background: gray;
	}
	.notif-displayer{
		width: 320px;
		height: auto;
		max-height: 380px;
		position: absolute;
		right: 10px;
		top: 8px;
		margin: 0;
		background: #2e2e2e;
		border-radius: 5px;
		text-align: left;
		overflow-y: auto;
		border: 8px solid #2e2e2e;
	}
	.notif-displayer::-webkit-scrollbar{
		width: 4px;
		border-radius: 3px;
	}
	.notif-displayer::-webkit-scrollbar-track{
		background: transparent;
	}
	.notif-displayer::-webkit-scrollbar-thumb{
		background: darkgrey;
		border-radius: 3px;
	}
	hr{
		width: 96%;
		height: 2px;
		border-radius: 2px;
		margin: 3px auto;
		margin-bottom: 6px;
	}
	.notif-displayer h3{
		font-size: 22px;
		color: silver;
		padding: 5px;
	}
	.notif-displayer::before{
		content: '';
		width: 10px;
		height: 10px;
		background: #2e2e2e;
		transform: rotate(45deg);
		position: absolute;
		right: 14px;
		top: -5px;
	}
	.notification{
		width: 100%;
		height: auto;
		padding: 6px;
		border-radius: 5px;
		display: flex;
		align-items: center;
		gap: 6px;
		cursor: pointer;
		padding-left: 8px;
	}
	.notification:hover{
		background: rgba(0, 0, 0, 0.40);
	}
	.notification .notif-avatar{
		width: 36px;
		height: 36px;
		border-radius: 50%;
		background: aliceblue;
		overflow-y: hidden;
	}
	.notification .notif-avatar img{
		width: 100%;
		height: 100%;
	}
	.notification .notif-text{
		width: auto;
		height: auto;
		padding: 5px;
	}
	.notification .notif-text h4{
		font-size: 15px;
		color: darkgrey;
		font-weight: 500;
	}
	.notification .notif-text p{
		font-size: 13px;
		color: dimgrey;
		display: -webkit-box;
		-webkit-line-clamp: 1;
		-webkit-box-orient: vertical;
		overflow: hidden;
	}
	@media(max-width: 576px){
		.lists-header{
			padding: 20px 10px;
		}
		.lists-header h4{
			font-size: 15px;
			display: none;
		}
		.lists-header .search-bar input{
			font-size: 16px;
			width: 200px;
			height: 30px;
			padding: 8px 20px 8px 10px;
		}
		.notif{
			padding: 3px;
		}
	}
</style>