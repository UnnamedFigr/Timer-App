<script>
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
    
    export let timerCount;
    export let mode = 'countdown';
    export let initialCountdown = 60;
    let time = mode === 'countdown' ? initialCountdown * 1000 : 0;
    let isRunning = false;
    let interval = null;
    let countdownInput = initialCountdown;
    
    const formatInput = (seconds) => {
        const hrs = Math.floor(seconds / 3600);
        const mins = Math.floor((seconds % 3600) / 60);
        const secs = seconds % 60;
        return `${hrs.toString().padStart(2, '0')}:${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
    }
    let inputDisplay = formatInput(countdownInput);
    const formatTime = (ms) => {
        //we will use modulo for the remainder
        //then we devide the modulo by 1000 because the remainder is in miliseconds 
        //and we are left with seconds (1000ms = 1s) 
        //then for the miliseconds we get the remainder of miliseconds so we divide by 1000
        const hours = Math.floor((ms / (1000 * 60 * 60)));
        const minutes = Math.floor((ms % ((1000 * 60 * 60)) / 60000 ));
        const seconds = Math.floor(((ms % (1000 * 60) / 1000)));
        const miliseconds =Math.floor((ms % (1000) / 10));

        return `${hours.toString().padStart(2, '0')} 
                : ${minutes.toString().padStart(2, '0')}
                : ${seconds.toString().padStart(2, '0')}
                . ${miliseconds.toString().padStart(2, '0')}`
    };

    const parseInput = (value) => {
        const parts = value.split(':').map(Number);
        if (parts.length !== 3 || parts.some(isNaN)) return countdownInput;
        const [hrs, mins, secs] = parts;
        if (hrs < 0 || mins < 0 || mins > 59 || secs < 0 || secs > 59) return countdownInput;
        return hrs * 3600 + mins * 60 + secs;
    }
    const handleInputChange = (e) => {
        inputDisplay = e.target.value;
        const seconds = parseInput(inputDisplay);
        if (seconds > 0)
        {
            countdownInput = seconds;
        }
            
    }

    $: inputDisplay = formatInput(countdownInput);

    const startTimer = () => {
        if(!isRunning){
            isRunning = true;
            if(mode === 'chronometer') 
            {
                const startTime = Date.now() - time;
                interval = setInterval(() => {
                    time = Date.now() - startTime;
                }, 10);
            }
            else {
                if(time === 0) {
                    time = initialCountdown * 1000;
                }
                time = countdownInput !== initialCountdown ? countdownInput * 1000 : initialCountdown * 1000;
                interval = setInterval(() => {
                    time -= 10;
                    if(time <= 0){
                        time = 0;
                        stopTimer();
                    }
                }, 10);
            }
        }
    }
    const stopTimer = () => {
        if(isRunning){
            isRunning = false;
            clearInterval(interval);
            interval = null;
        }        
    }
    const resetTimer = () => {
        stopTimer();
        time = mode === 'countdown' ? initialCountdown * 1000 : 0;
    }

</script>

<div class="timer-block">
    <h1>Timer {timerCount} ({mode})</h1>
    {#if mode === 'countdown'}
        <input
            type="text"
            bind:value={inputDisplay}
            on:input={handleInputChange}
            min="1"
            disabled={isRunning}
            pattern="\d{2}:\d{2}:\d{2}"
        >
    {/if}
    <div class="display">{formatTime(time)}</div>
    <div class="controls">
        <button on:click={startTimer} disabled={isRunning}>START</button>
        <button on:click={stopTimer} disabled={!isRunning}>STOP</button>
        <button on:click={resetTimer}>RESET</button>
    </div>
    <button on:click={() => dispatch('remove', timerCount)}>X</button>
</div>
<style>

    .timer-block{
        border: 1px solid black;
        width: 65%;
        margin: 15px auto;
    }
    .timer-block > h1 {
        font-size: 1.5rem;
        margin: 8px auto;
    }
    .display {
        margin: 10px auto;
        font-size: 1.95rem;
    }
    .controls{
        width: 100%;
        display: flex;
        flex-direction: row;
        justify-content: space-evenly;
    }
    .controls ~ button {
        width: 40px;
        height: 40px;
        margin: 7px auto;
        display: block;
        border-radius: 50%;
        background-color: red;
        color: white;
        font-weight: bolder;
        font-size: 18px;
}
</style>