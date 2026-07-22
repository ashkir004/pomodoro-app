<script lang="ts">
    import { Tween } from "svelte/motion";
    import { linear } from "svelte/easing";

    const WORK_DURATION = 5 * 60; // 5 mins > 300 seconds
    let totalSeconds = WORK_DURATION;
    let secondsLeft = $state(totalSeconds);
    let timerId = $state<number | undefined>(undefined);
    let endTime = $state(0);
    let isRunning = $derived(timerId !== undefined);
    let progress = $derived(new Tween(1, { duration: 400, easing: linear }).current);
    let progressDegrees = $derived(progress * 360);
    let capRotation = $derived(progressDegrees);

    function startTimer() {
        if (isRunning) return;

        endTime = Date.now() + (secondsLeft * 1000);

        timerId = setInterval(() => {
            const now = Date.now();
            const remainingMs = endTime - now;

            if (remainingMs <= 0) {
                completeTimer();
            } else {
                secondsLeft = Math.ceil(remainingMs / 1000);
                progress = secondsLeft / totalSeconds;
            }
        }, 200);
    }

    $effect(() => {
        if (secondsLeft <= 0) {
            return alert("Time's up!");
        }

        startTimer();
    });

    function pauseTimer() {
        if (timerId !== undefined) {
            clearInterval(timerId);
            timerId = undefined;
        }
    }

    function resetTimer() {
        pauseTimer();
        secondsLeft = WORK_DURATION;
        progress = 1;
    }

    function completeTimer() {
        pauseTimer();
        secondsLeft = 0;
        progress = 0;
        alert("Time to take a break!");
    }


</script>

<main class="clock flex flex-col items-center justify-center rounded-full p-4
    bg-radial-[at_80%] from-surface-light/30 to-surface-darkest">
        <div class="grid grid-cols-1 grid-rows-1 items-center justify-center rounded-full p-3 gap-8 bg-surface-darkest">
            
                <div data-theme="red"
                    class="visual-ring relative w-64 md:w-72 aspect-square rounded-full flex items-center justify-center p-2" 
                    style="--progress-angle: {progressDegrees}deg;">

                    <div data-theme="red" class="absolute w-2 h-2 rounded-[50%] z-10
                    top-0 left-1/2 -translate-x-1/2"></div>

                    <div data-theme="red" class="absolute w-2 h-2 rounded-[50%] z-10
                    top-0 left-1/2 origin-[50%_1600%] md:origin-[50%_1800%] -translate-x-1/2"
                    style="transform: rotate({capRotation}deg);"></div>

                    <div class="inner-mask bg-surface-darkest flex flex-col items-center justify-center rounded-full w-full h-full">
                        <div class="time-display text-on-surface flex flex-col gap-2 items-center justify-center outline-1">
                            <h1 class="text-[5rem] md:text-[6rem] font-sans font-bold">
                                {Math.floor(secondsLeft / 60)}:{String(secondsLeft % 60).padStart(2, '0')}
                            </h1>
                            <button class="text-sm font-extrabold uppercase tracking-[.5rem] active:scale-95 transition-transform" >Pause</button>
                        </div>
                    </div>
                </div>
        </div>
</main>

<style>
    .visual-ring[data-theme="red"] {
        will-change: background;
        background: conic-gradient(
            currentColor 0deg var(--progress-angle),
            var(--color-surface-darkest) var(--progress-angle) 360deg);
    }

    .clock {
        box-shadow: 30px 30px 80px 10px rgba(0, 0, 0, 0.2), 
                    -30px -20px 80px 10px rgba(63, 102, 241, 0.12);
    }

    

    /* * {
        outline: 1px solid yellow;
    } */
</style>