<script>
	/*
		This is just a default page when no chat is selected
	*/
	import { onMount } from 'svelte';

    let messages = [];

    onMount(async () => {
        // const response = await fetch('/api/chat/messages/1');
        // messages = await response.json();
    });
</script>

<div class="chat-content">
			<p class="info">no room selected</p>
</div>
<div class="chat-users-content"></div>

<style>

	.chat-content {
		width: 100%;
		/* max-height: 60vh; */
		margin: 30px 0;
	}

	.chat-users-content
	{
		background-color: #404040;
		width: 30vw;
	}
/* 
	.component_send_box {
		border: solid 1px lightseagreen;
		border-radius: 50px;
		overflow: hidden;
		margin: 0px 20px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: baseline;
	}

	.component_send_box input {
		border: none;
		width: 100%;
		max-width: 80%;
	}

	input:focus {
    	outline: none;
	}

	.component_send_box button {
		border: none;
		border-radius: 0px;
		width: 100px;
		height: 100%;
		background: lightseagreen;
	} */
/* 
	.messages {
		max-height: 50vh;
		overflow: scroll;
	} */

	.info {
		width: 100%;
		color: gray;
		text-align: center;
	}
</style>