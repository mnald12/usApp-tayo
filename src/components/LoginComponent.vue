<template>
	<h3 class="introText">usApp Tayo</h3>
	<p class="introText-p">Simple Chat App</p>
	<div class="signIn-container">
		<button @click="loginSubmit">Login with Google <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-google" viewBox="0 0 16 16"><path d="M15.545 6.558a9.42 9.42 0 0 1 .139 1.626c0 2.434-.87 4.492-2.384 5.885h.002C11.978 15.292 10.158 16 8 16A8 8 0 1 1 8 0a7.689 7.689 0 0 1 5.352 2.082l-2.284 2.284A4.347 4.347 0 0 0 8 3.166c-2.087 0-3.86 1.408-4.492 3.304a4.792 4.792 0 0 0 0 3.063h.003c.635 1.893 2.405 3.301 4.492 3.301 1.078 0 2.004-.276 2.722-.764h-.003a3.702 3.702 0 0 0 1.599-2.431H8v-3.08h7.545z"/></svg></button>
	</div>
</template>
<script>
	import firebase from 'firebase/compat/app'
	import 'firebase/compat/auth'
	import 'firebase/compat/firestore'
	export default{
		name : 'LoginComponent',
		methods : {
			loginSubmit(){
				firebase.auth().setPersistence(firebase.auth.Auth.Persistence.NONE);
				const provider = new firebase.auth.GoogleAuthProvider()
				firebase.auth().signInWithRedirect(provider)
				.catch(console.log)
			}
		}
	}
</script>
<style type="text/css">
	.signIn-container{
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		margin: 80px auto;
	}
	.signIn-container button{
		width: auto;
		height: auto;
		border: none;
		outline: none;
		padding: 10px 20px;
		color: aliceblue;
		background: #3e3e3e;
		cursor: pointer;
		border-radius: 4px;
		font-size: 20px;
		transition: 0.50s;
		display: flex;
		align-items: center;
		gap: 10px;
	}
	.signIn-container button:hover{
		transform: translateY(-3px);
		box-shadow: -1px 2px 3px 0 #3e3e3e;
	}
	.introText{
		margin-top: 50px;
		font-size: 100px;
		color: #3e3e3e;
	}
	.introText-p{
		font-size: 16px;
	}
	@media (max-width : 576px){
		.introText{
			margin-top: 120px;
			font-size: 60px;
		}
		.introText-p{
			font-size: 15px;
		}
	}
</style>