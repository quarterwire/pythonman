<script lang="ts">
	import { methods } from '$lib/stores/methods.js';
	let dropDownVisible: boolean = $state(false);
	let activeMethod: string = $state('GET');
	let inputValue: string = $state('');
	import { HighlightAuto, LineNumbers } from 'svelte-highlight';
	import horizonDark from 'svelte-highlight/styles/horizon-dark';
	function toggleVisible() {
		dropDownVisible = !dropDownVisible;
	}
	function setActiveMethod(method: string) {
		activeMethod = method;

		dropDownVisible = false; // Close dropdown after selecting
	}

	let code = $state('');
	let hlRef: HighlightAuto;
	async function handleRequest() {
		switch (activeMethod) {
			case 'GET':
				const res = await fetch(inputValue);
				code = await res.json();
				code = JSON.stringify(code, null, 2);
				hlRef.highlightCode();
		}
	}
</script>

<svelte:head>
	{@html horizonDark}
</svelte:head>
<main>
	<div class=" flex h-screen flex-col items-center justify-center gap-10">
		<div class="flex items-center justify-center">
			<form class="flex border border-gray-200">
				<div class="relative inline-block text-left">
					<!-- Dropdown Button -->
					<button
						type="button"
						onclick={toggleVisible}
						class="inline-flex h-full w-40 items-center justify-between rounded-md rounded-r-none border border-gray-300 p-4 text-lg font-semibold {methods[
							activeMethod
						]} "
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
									onclick={() => setActiveMethod(method)}
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
					bind:value={inputValue}
					class="w-[50vw] rounded-md rounded-l-none border-2 border-gray-500 p-2 focus:{methods[
						activeMethod
					]} focus:outline-none"
					placeholder="URL"
				/>
			</form>
			<button
				onclick={handleRequest}
				class="duration-350 mx-2 cursor-pointer rounded-md border-gray-300 bg-blue-400 p-5 px-8 font-semibold text-white transition hover:bg-blue-500"
				>Send</button
			>
		</div>
		<div class="flex h-40 flex-col items-start overflow-y-auto bg-[#1d1d1d]">
			<div>
				<ul class="flex flex-row text-xs">
					<li class="bg-[#1d1d1d] p-3 px-6 text-white">Body</li>
					<li class="bg-[#1d1d1d] p-3 px-6 text-white">Headers</li>
					<li class="bg-[#1d1d1d] p-3 px-6 text-white">Cookies</li>
				</ul>
			</div>
			<HighlightAuto bind:this={hlRef} {code} let:highlighted>
				<LineNumbers
					{highlighted}
					--line-number-color="rgba(255, 255, 255, 0.3)"
					--border-color="rgba(255, 255, 255, 0.1)"
					--padding-left="2em"
					--padding-right="1em"
					wrapLines
					class="h-full w-[62vw] overflow-y-auto"
				/></HighlightAuto
			>
		</div>
	</div>
</main>
