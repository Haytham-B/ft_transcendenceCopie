<script lang='ts'>
	import { goto } from '$app/navigation';
    import { client, connectClientToColyseus, joinGame, roomData, resetroomData } from '$lib/gamesocket.js'
	import { socket } from '$lib/socketsbs.js';
    import { onDestroy, onMount } from 'svelte';
    import { game_mode, userId } from '$lib/stores'

export let data;

/**
 * GameStates
 */
let name: any;
let input: string;
let initialScreen: any;
let canvas: any;
let ctx: any;
let game: any;
let pong: any;
let room: any;
let matchroom: any;
let gameActive: boolean = false;
let promise: any;
let gameCode: any;
let playerNumber: number;
let changecolor: boolean = false
;

let quitGame = () =>
{
    goto("/app/game");
}

onMount(async () =>
{
    if (!roomData)
    {
        console.log("joining room");
        //connectClientToColyseus();
        await joinGame(data.gameId);
        playerNumber = 2;
    }
    else
        playerNumber = 1;
    // if (roomData)
    // {
    //     roomData.onMessage("init", (j: number) => {
    //         playerNumber = j;
    //         console.log(playerNumber);
    //         //console.log('test init22! ' + name + ' ' + roomData.id);
    //         //console.log("client id: " + client.id);
    //         console.log('init');
    //     });

    // }
    socket.emit('startGame');
    init();
    createHandlers();
    socket.on('refused', quitGame);
});

onDestroy(() =>
{
    document.removeEventListener('keyup', keyup);
    document.removeEventListener('keydown', keydown);
    socket.emit('endGame');
    resetroomData();
    // client.close();
});

let createHandlers = () =>
{
    roomData.onMessage("gameState", (gameState: any) => 
	{
        //console.log('test gameState');
        gameState = JSON.parse(gameState);
        requestAnimationFrame(() => trender(gameState));
    });
    roomData.onMessage("gameOver", (data: any) => {
        document.removeEventListener('keyup', keyup);
        document.removeEventListener('keydown', keydown);
        socket.emit('endGame');
		let date = JSON.parse(data);
    	if (date.winner === playerNumber) {
            resetroomData();
        	alert('You win!');
			
    	}
    	else {
            resetroomData();
      		alert('You lose!');
    	}
	});
}

function init() {
    console.log("dans init");
    //initialScreen.style.display = "none";
    //game.style.display = "block";
    canvas = pong;
    // const { width, height } = canvas.getBoundingClientRect();
    // canvas.width = width;
    // canvas.height = height;
    // console.log(canvas.width);
    // console.log(canvas.height);
    ctx = canvas.getContext('2d');
    // gameCode = document.getElementById('gameCode');
    // gameCode.innerText = name;
    gameActive = true;
    // console.log(gameCode);
    document.addEventListener('keydown', keydown);
    document.addEventListener('keyup', keyup);
    //gameActive = true;
}

    function keydown(e: any) {
		console.log("keydown ", e.keyCode);
		if (e.keyCode === 38) {
			if (playerNumber == 1)
				roomData.send("keydown38player1");
			else
				roomData.send("keydown38player2");
		}
		if (e.keyCode === 40) {
			if (playerNumber == 1)
				roomData.send("keydown40player1");
			else
				roomData.send("keydown40player2");
		}
	}

	function keyup(e: any) {
		console.log("keyup ", e.keyCode);
		if (e.keyCode === 38) {
			if (playerNumber == 1)
				roomData.send("keyup38player1");
			else
				roomData.send("keyup38player2");
		}
		if (e.keyCode === 40) {
			if (playerNumber == 1)
				roomData.send("keyup40player1");
			else
				roomData.send("keyup40player2");
		}
	}
/**
 * 
 * GAME RENDER
 * 
*/

function drawRect(x: number, y: number, w: number, h: number, color: any) {
    if (ctx) {
        ctx.fillStyle = color;
        ctx.fillRect(x, y, w, h);
    }
}

function drawArc(x: number, y: number, r: number, color: any) {
    if (ctx) {
        ctx.fillStyle = color;
        ctx.beginPath();
        ctx.arc(x, y, r, 0, Math.PI * 2, true);
        ctx.closePath();
        ctx.fill();
    }
}


function drawNet(statenet: any, color: any) {
    for (let i = 0; i <= canvas.height; i += 15) {
//        drawRect(statenet.net.x, statenet.net.y + i, statenet.net.width, statenet.net.height, statenet.net.color);
		drawRect(statenet.net.x, statenet.net.y + i, statenet.net.width, statenet.net.height, color);

    }
}

function drawText(text: string, x: number, y: number, color: any) {
    if (ctx) {
        //ctx.fillStyle = "#FFF";
		ctx.fillStyle = color;
        ctx.font = "75px fantasy";
        ctx.fillText(text, x, y);
    }
}


function trender(state: any) {
	if (changecolor === false)
		drawRect(0, 0, canvas.width, canvas.height, "#000");
	else
    	drawRect(0, 0, canvas.width, canvas.height, "#FFF");

    	drawText(state.user.score, canvas.width / 4, canvas.height / 5, "#FF0000");

    	drawText(state.com.score, 3 * canvas.width / 4, canvas.height / 5, "#FF0000");
	if (changecolor === false)
    	drawNet(state, "#FFF");
	else
		drawNet(state, "#000");

	if (changecolor === false)
		drawRect(state.user.x, state.user.y, state.user.width, state.user.height, "#FFF");
	else
    	drawRect(state.user.x, state.user.y, state.user.width, state.user.height, "#000");

	if (changecolor === false)
		drawRect(state.com.x, state.com.y, state.com.width, state.com.height, "#FFF");
	else
   		drawRect(state.com.x, state.com.y, state.com.width, state.com.height, "#000");

	if (changecolor === false)
    	drawArc(state.ball.x, state.ball.y, state.ball.radius, "#FFF");
	else
		drawArc(state.ball.x, state.ball.y, state.ball.radius, "#000");
}

//------------------------------------------------------------------------------------------------



</script>
<p>{data.gameId} $$$ {playerNumber}</p>
<canvas bind:this={pong} id="pong" width=600 height=400></canvas>


<style>
    #pong {
        border: 2px solid #FFF;
        position: relative;
        margin: auto;
        top: 0;
        right: 0;
        left: 0;
        bottom: 0;
    }
</style>