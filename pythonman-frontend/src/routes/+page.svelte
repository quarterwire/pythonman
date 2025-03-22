<script lang="ts">
	import { methods } from '$lib/stores/methods.js';
	let dropDownVisible: boolean = false;
	let activeMethod: string = 'GET';

	function toggleVisible() {
		dropDownVisible = !dropDownVisible;
	}

	function setActiveMethod(method: string) {
		activeMethod = method;

		dropDownVisible = false; // Close dropdown after selecting
	}
</script>

<main>
	<div class="flex h-screen items-center justify-center">
		<form class="flex border border-gray-200">
			<div class="relative inline-block text-left">
				<!-- Dropdown Button -->
				<button
					type="button"
					on:click={toggleVisible}
					class="inline-flex h-full w-40 items-center justify-between rounded-md rounded-r-none border-r p-4 text-lg font-semibold {methods[
						activeMethod
					]} border-gray-200"
					id="menu-button"
					aria-expanded={dropDownVisible}
					aria-haspopup="true"
				>
					{activeMethod}
					<svg
						class="-mr-1 size-5 text-gray-400"
						viewBox="0 0 20 20"
						fill="currentColor"
						aria-hidden="true"
					>
						<path
							fill-rule="evenodd"
							d="M5.22 8.22a.75.75 0 0 1 1.06 0L10 11.94l3.72-3.72a.75.75 0 1 1 1.06 1.06l-4.25 4.25a.75.75 0 0 1-1.06 0L5.22 9.28a.75.75 0 0 1 0-1.06Z"
							clip-rule="evenodd"
						/>
					</svg>
				</button>

				<!-- Dropdown Menu -->
				{#if dropDownVisible}
					<div class="absolute left-0 z-10 mt-1 w-40 rounded-md bg-gray-200 shadow-lg">
						{#each Object.entries(methods) as [method, color]}
							<button
								class:bg-gray-300={activeMethod === method}
								class="w-full cursor-pointer p-4 text-left hover:bg-gray-300 {color} font-semibold"
								on:click={() => setActiveMethod(method)}
							>
								{method}
							</button>
						{/each}
					</div>
				{/if}
			</div>

			<!-- Input Field -->
			<input
				name="URL"
				class="w-[50vw] rounded-md rounded-l-none border-2 border-gray-500 p-2 focus:{methods[
					activeMethod
				]} focus:outline-none"
				placeholder="URL"
			/>
		</form>
		<button class="mx-2 rounded-md border-gray-300 bg-blue-400 p-5 px-8 font-semibold text-white"
			>Send</button
		>
	</div>
</main>
