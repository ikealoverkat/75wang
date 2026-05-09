<script>
	import { text } from '@sveltejs/kit';

	let tasks = $state([]);
	let newTask = $state('');
	let InputElement = $state();
	let placeholder = $state();

	function addTask(e) {
		if (e) e.preventDefault();

		if (newTask.trim() !== '') {
			tasks = [...tasks, { id: Date.now(), text: newTask, completed: false }];
			newTask = '';
		}
		InputElement.focus();
		if (placeholder.style.display !== 'none') placeholder.style.display = 'none';
	}
</script>

<div class="h-screen w-screen place-items-center overflow-x-hidden bg-pink-50 p-12">
	<img src="/75wlogo.png" alt="75wang logo" class="h-35 w-auto duration-300 hover:scale-102" />
	<div class="m-4 flex flex-row gap-2">
		<!-- <h1 class="font-kisba text-4xl text-pink-950 duration-200 hover:p-1"><i>/75wæŋ/:</i></h1> -->
		<h2 class="m-2.5 font-kisba text-2xl text-pink-900 duration-200 hover:p-1">
			<b>75wang -</b> a customizable 75 hard tracker
		</h2>
	</div>

	<!-- <input placeholder="enter goals here..." type="checkbox" /> -->
	<form id="task-form" class="flex w-[40%] flex-row justify-between p-2" onsubmit={addTask}>
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

	<ul class="p-8 font-hina text-2xl text-pink-900 duration-300">
		<p bind:this={placeholder} class="text-pink-900/40 italic">no goals yet. add one!</p>
		{#each tasks as task (task.id)}
			<li class="flex flex-row gap-10">
				<input
					type="checkbox"
					class="mt-1 h-6 w-6 appearance-none rounded-md border-2 border-pink-800/20 bg-linear-to-b from-white to-pink-good/230 duration-200 checked:border-pink-800 checked:bg-pink-800 hover:scale-105"
					onclick={() => {
						task.completed = !task.completed;
					}}
				/>
				<div class="relative w-fit">
					<span>{task.text}</span>
					<span
                    class="absolute -left-1 top-1/2 h-0.5 w-full origin-left bg-pink-900/95 transition-transform duration-300"
                    class:scale-x-120={task.completed}
                    class:scale-x-0={!task.completed}
                >
					</span>
				</div>
			</li>
		{/each}
	</ul>
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
