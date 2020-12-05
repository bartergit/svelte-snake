<script lang="ts">
	function getRandomInt(max) {
		return Math.floor(Math.random() * Math.floor(max));
	}
	function isSnake(pos): boolean {
		for (let i = 1; i < snake.length; i++) {
			if (snake[i].x == pos.x && snake[i].y == pos.y) {
				return true;
			}
		}
		return false;
	}
	function spawnFood() {
		let a = getRandomInt(N);
		let b = getRandomInt(N);
		if (grid[a][b] == 1) {
			spawnFood();
		} else {
			grid[a][b] = 2;
		}
	}
	function validate(pos) {
		if (pos.x >= N) pos.x = 0;
		if (pos.y >= N) pos.y = 0;
		if (pos.x < 0) pos.x = N - 1;
		if (pos.y < 0) pos.y = N - 1;
	}
	function update() {
		let tail = snake[0];
		let head = snake[snake.length - 1];
		let newHead = { x: head.x + currentDir.x, y: head.y + currentDir.y };
		validate(newHead);
		if (isSnake(newHead)) {
			state = "You LOST!";
			clearInterval(updater);
			return;
		}
		if (grid[newHead.y][newHead.x] == 2) {
			score += 1;
			spawnFood();
		} else {
			snake.shift();
			grid[tail.y][tail.x] = 0;
		}
		snake.push(newHead);
		grid[newHead.y][newHead.x] = 1;
	}
	//init variables
	let currentDir = { x: 1, y: 0 };
	let snake: { x: number; y: number }[] = [
		{ x: 5, y: 5 },
		{ x: 4, y: 5 },
	];
	const N = 18;
	let grid = new Array(N);
	for (let i = 0; i < N; i++) {
		grid[i] = new Array(N);
		for (let j = 0; j < N; j++) {
			grid[i][j] = 0;
		}
	}
	for (let ind in snake) {
		grid[snake[ind].y][snake[ind].x] = 1;
	}
	spawnFood();
	const fps = 8;
	let state = "";
	let score = 0;
	let updater = setInterval(update, 1000 / fps);
	//
	document.addEventListener("keydown", function (event) {
		switch (event.key) {
			case "ArrowLeft":
				if (currentDir.x != 1) currentDir = { x: -1, y: 0 };
				break;
			case "ArrowRight":
				if (currentDir.x != -1) currentDir = { x: 1, y: 0 };
				break;
			case "ArrowDown":
				if (currentDir.y != -1) currentDir = { x: 0, y: 1 };
				break;
			case "ArrowUp":
				if (currentDir.y != 1) currentDir = { x: 0, y: -1 };
				break;
		}
	});
</script>

<style>
	h1,
	p {
		font-family: "Cascadia Mono";
	}
	.line {
		display: flex;
	}
	.el {
		/* 		display: inline; */
		width: 30px;
		height: 30px;
		/* border: 0.5px solid black; */
		margin: 1px;
	}
	.blue {
		background-color: lightblue;
	}
	.snake {
		background-color: green;
	}
	.food {
		background-color: rebeccapurple;
	}
	.container {
		margin: auto;
		width: 600px;
	}
</style>

<div class="container">
	<h1>Snake game on svelte WOW</h1>
	<p>Score: {score} {state}</p>
	{#each grid as line}
		<div class="line">
			{#each line as val}
				<div
					class="el"
					class:blue={val == 0}
					class:snake={val == 1}
					class:food={val == 2} />
			{/each}
		</div>
	{/each}
</div>
