<script>
	import Timer from './routes/Timer.svelte'
	export let name;

	let timerCount = 1;
	let timers = [{id: 1, mode: 'chronometer'}]
	let showPopup = false;
	let selectedMode;

	const openPopup = () => {
		showPopup = true;
		selectedMode = 'chronometer';
	}
	const cancelPopup = () => {showPopup = false};
	const addTimer =() => {
		timerCount += 1;
		timers = [...timers, {id: timerCount, mode: selectedMode}];
	}

</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
	<div id="timer-container">
		{#each timers as timer (timer.id)}
		<Timer
			timerCount = {timer.id}
			mode = {timer.mode} 
			on:remove = {() => timers = timers.filter(t => t.id !== timer.id)}/>
		{/each}
		<button id="add-button">Add Timer</button>
	</div>
</main>

<style>
	*{
		margin: 0;
		padding: 0;
	}
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}
	#timer-container{
        border: 1px solid black;
        width: 450px;
        margin: 50px auto auto auto;
        height: 400px;
        display: flex;
        flex-direction: column;
        overflow-y: scroll;
    }
	#add-button{
		display:flex;
		margin: 10px auto;
	}
	
	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 480px) {
		main {
			max-width: none;
		}
	}
</style>