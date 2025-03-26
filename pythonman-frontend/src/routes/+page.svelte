<script lang="ts">
	import { methods } from '$lib/stores/methods';
	import { HighlightAuto, LineNumbers } from 'svelte-highlight';
	import horizonDark from 'svelte-highlight/styles/horizon-dark';

	let dropDownVisible = false;
	let activeMethod = 'GET';
	let inputValue = '';
	let code = '';
	let hlRef: HighlightAuto;

	function toggleVisible() {
		dropDownVisible = !dropDownVisible;
	}

	function setActiveMethod(method: string) {
		activeMethod = method;
		dropDownVisible = false;
	}

	async function handleRequest() {
		if (activeMethod === 'GET') {
			const res = await fetch(inputValue);
			code = JSON.stringify(await res.json(), null, 2);
			code = `${code}`;
		}
	}
</script>

<svelte:head>
	{@html horizonDark}
</svelte:head>

<main class="grid h-screen grid-cols-5 grid-rows-[8%_auto_30%] overflow-hidden">
	<!-- Navbar -->
	<div class="col-span-5 flex items-center justify-center bg-gray-800 text-lg font-bold text-white">
		Header
	</div>

	<!-- Sidebar -->
	<div class="col-span-1 row-span-2 bg-gray-700 p-4 text-white">Sidebar</div>

	<!-- Main Content -->
	<div class="col-span-4 p-4">
		<div class="flex items-center">
			<form class="flex w-full border border-gray-200">
				<div class="relative inline-block text-left">
					<button
						type="button"
						onclick={toggleVisible}
						class="inline-flex h-full w-40 items-center justify-between rounded-md rounded-r-none border border-r-0 border-gray-300 p-4 text-lg font-semibold {methods[
							activeMethod
						]}"
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

				<input
					name="URL"
					bind:value={inputValue}
					class="w-full rounded-md rounded-l-none border-2 border-gray-200 p-2 focus:{methods[
						activeMethod
					]} focus:outline-none"
					placeholder="URL"
				/>
			</form>
			<button
				onclick={handleRequest}
				class="mx-2 cursor-pointer rounded-md border-gray-300 bg-blue-400 p-5 px-8 font-semibold text-white transition hover:bg-blue-500"
			>
				Send
			</button>
		</div>
	</div>

	<!-- JSON Response Section -->
	<div class="col-span-4 overflow-hidden bg-[#1d1d1d] p-4">
		<div class="flex h-full flex-col items-start">
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
					class="h-full overflow-y-auto"
				/>
			</HighlightAuto>
		</div>
	</div>
</main>
