<script>
	import { text } from '@sveltejs/kit';
	import { onMount } from 'svelte';
	import dayjs from 'dayjs';
	dayjs().format();
	import dayOfYear from 'dayjs/plugin/dayOfYear';
	dayjs.extend(dayOfYear);

	let tasks = $state([]);
	let newTask = $state('');
	let InputElement = $state();
	let placeholder = $state();

	let currentDay = $state(0);
	let startingDate = $state();

	let days = $state(
		Array.from({ length: 75 }, (_, day) => ({
			id: day,
			completion: false
		}))
	);

	let areYouSureDiv = $state();

	onMount(() => {
		let storedTasks = localStorage.getItem('tasks');
		if (storedTasks) {
			tasks = JSON.parse(storedTasks);
			if (tasks.length > 0) {
				placeholder.style.display = 'none';
			}
		}
		currentDay = dayjs().diff(startingDate, 'day');
        startingDate = dayjs("2026-05-08");
        // startingDate = dayjs(localStorage.getItem('startingDate'));
		console.log(currentDay);
		console.log(startingDate);
	});

	function addTask(e) {
		if (e) e.preventDefault();

		if (newTask.trim() !== '') {
			tasks = [...tasks, { id: Date.now(), text: newTask, completed: false }];
			newTask = '';
		}
		InputElement.focus();

		if (placeholder.style.display !== 'none') placeholder.style.display = 'none';

		localStorage.setItem('tasks', JSON.stringify(tasks));
		console.log($state.snapshot(tasks));
	}

	$effect(() => {
		const allDone = tasks.length > 0 && tasks.every((t) => t.completed);

		if (days[currentDay]?.completion !== allDone) {
			days[currentDay].completion = allDone;
			localStorage.setItem('days', JSON.stringify(days));
		}
	});
</script>

<div class="flex h-screen w-screen flex-col place-items-center overflow-x-hidden bg-pink-50 p-12">
	<!-- title card -->
	<div class="flex w-1/3 flex-row items-center">
		<img
			src="/75wlogo.png"
			alt="75wang logo"
			class="h-35 w-auto p-0.5 duration-300 hover:scale-102 hover:p-0"
		/>
		<div class="m-4 flex flex-col">
			<!-- <h1 class="font-kisba text-4xl text-pink-950 duration-200 hover:p-1"><i>/75wæŋ/:</i></h1> -->
			<h1 class="font-kisba text-4xl text-pink-950 duration-200 hover:p-1"><i>/七十五王/:</i></h1>
			<h2 class="font-kisba text-2xl text-pink-900 duration-200">
				<b>75wang:</b> a customizable 75 hard tracker
			</h2>
		</div>
	</div>

	<div class="flex h-full flex-row items-start justify-center">
		<!-- tasks -->
		<div class="w-full">
			<!-- add tasks -->
			<form id="task-form" class="flex flex-row justify-between p-2" onsubmit={addTask}>
				<input
					type="text"
					id="task-input"
					bind:value={newTask}
					bind:this={InputElement}
					placeholder="enter goals here..."
					class="mb-4 w-full p-2 px-6 font-hina text-[22px] text-pink-950 duration-300 hover:p-3 focus:outline-none"
					autocomplete="off"
				/>
				<button
					type="submit"
					class="mb-4 ml-4 hidden px-4 font-hina text-[22px] font-bold text-pink-800 duration-300 hover:scale-110 hover:rotate-10"
					>+</button
				>
			</form>

			<!-- task list -->
			<ul class="p-8 font-hina text-2xl text-pink-900 duration-300">
				<p bind:this={placeholder} class="text-pink-900/40 italic">no goals yet. add one!</p>
				{#each tasks as task (task.id)}
					<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
					<!-- svelte-ignore a11y_click_events_have_key_events -->
					<li
						class="flex flex-row gap-10 duration-300 hover:gap-11 hover:p-1 [&>button]:opacity-0 [&>button]:duration-300 hover:[&>button]:opacity-100"
						onclick={() => {
							task.completed = !task.completed;
							tasks = tasks;
						}}
					>
						<input
							type="checkbox"
							bind:checked={task.completed}
							class="mt-1 h-6 w-6 appearance-none rounded-md border-2 border-pink-800/20 bg-linear-to-b from-white to-pink-good/230 duration-200 checked:border-pink-800 checked:bg-pink-800 hover:scale-105"
						/>
						<div class="relative w-fit">
							<span>{task.text}</span>
							<span
								class="absolute top-1/2 -left-1 h-0.5 w-full origin-left bg-pink-900/95 transition-transform duration-300"
								class:scale-x-120={task.completed}
								class:scale-x-0={!task.completed}
							>
							</span>
						</div>
						<button
							onclick={() => {
								tasks.splice(tasks.indexOf(task), 1);
								if (tasks.length > 0) {
									placeholder.style.display = 'none';
								} else {
									placeholder.style.display = 'block';
								}
							}}
							class=" font-bold text-pink-900/40 hover:text-pink-900/70"
						>
							—
						</button>
					</li>
				{/each}
			</ul>
		</div>
		<!-- tracking half -->
		<div class="flex flex-col items-center">
			<!-- tracker -->
			<div class="m-8 flex w-1/2 flex-row flex-wrap gap-2">
				{#each days as day (day.id)}
					<div class="relative">
						<div
							class="absolute top-0 z-0 h-12 w-12 origin-bottom bg-pink-good transition-transform duration-200"
							class:scale-y-100={day.completion === true}
							class:scale-y-0={day.completion === false}
						></div>
						<!-- day -->
						<div
							class={day.id == currentDay
								? 'relative z-10 h-12 w-12 place-content-center bg-pink-good/20 text-center font-hina text-xl text-pink-900 shadow-sm shadow-pink-950/50 outline-2 outline-pink-900/50 duration-100 hover:scale-105'
								: 'relative z-10 h-12 w-12 place-content-center bg-pink-good/5 text-center font-hina text-xl text-pink-900 shadow-sm shadow-pink-800/30 outline outline-pink-900/50 duration-100 hover:scale-105'}
						>
							{day.id + 1}
						</div>
					</div>
				{/each}
			</div>
			<!-- start button -->
			<button
				onclick={() => {
					startingDate = dayjs();
					console.log(startingDate);
					localStorage.setItem('startingDate', startingDate);
                    this.classList.add('hidden');
				}}
				class="bg-linear-to-b from-white/50 to-pink-100/50 p-4 text-center font-hina text-xl font-bold text-pink-800/75 italic shadow-pink-800 outline outline-pink-800/20 duration-200 hover:m-2 hover:from-white hover:to-pink-100 hover:p-5 hover:shadow-sm hover:outline-pink-800/50 active:m-1 active:p-1"
			>
				start
			</button>
		</div>

		<!-- hours until end of day -->
		<!-- days -->
	</div>

	<!-- clear button -->
	<button
		onclick={() => {
			areYouSureDiv.style.display = 'flex';
		}}
		class="absolute bottom-20 bg-linear-to-b from-white/50 to-pink-100/50 p-2 text-center font-hina text-xl font-bold text-pink-800/75 italic shadow-pink-800 outline outline-pink-800/20 duration-200 hover:m-2 hover:from-white hover:to-pink-100 hover:p-3 hover:shadow-sm hover:outline-pink-800/50 active:m-1 active:p-1"
	>
		clear all progress
	</button>

	<!-- are you sure clear -->
	<div
		class="absolute top-0 z-20 flex h-screen w-screen bg-[#1F1010]/35 backdrop-blur-xs"
		bind:this={areYouSureDiv}
		style="display: none"
	>
		<div
			class="m-auto flex h-fit flex-col items-center bg-pink-100 p-24 pb-15 outline outline-pink-950"
		>
			<h1 class="font-kisba text-4xl text-pink-950">are you sure?</h1>
			<p class="m-2 mb-4 text-center font-hina text-2xl text-pretty text-pink-800">
				proceeding will completely clear your task list and progress.
			</p>
			<span class="m-4 flex gap-12">
				<button
					class="bg-pink-good p-2 font-hina text-xl text-pink-950 shadow-xs shadow-pink-950/50 outline outline-pink-950/50 duration-200 hover:scale-104 hover:bg-[#F7ADC7] hover:shadow-sm"
					onclick={(areYouSureDiv.style.display = 'none')}>no, go back</button
				>
				<button
					class="bg-[#BA6C8A] p-2 font-hina text-xl text-white shadow-xs shadow-pink-950/50 outline outline-pink-950/50 duration-200 hover:scale-104 hover:bg-[#AD637F] hover:shadow-sm"
					onclick={() => {
						localStorage.clear();
						tasks.length = 0;
						placeholder.style.display = 'block';
						areYouSureDiv.style.display = 'none';
					}}>OK</button
				>
			</span>
		</div>
	</div>
</div>

<h3 class="m-2 px-4 text-left font-hina text-xl text-pink-950">
	made w/★ by kat wang
	<br />75wang is open source (add link later)
	<br /> last updated may 2026
</h3>
<!-- 
	<h3 class="font-hina text-xl text-pink-950">
		i made this solely for myself but why not i guess anyone can use it
	</h3> -->
