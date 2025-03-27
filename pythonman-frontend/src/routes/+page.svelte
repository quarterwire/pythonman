<script lang="ts">
	import { methods } from '$lib/stores/methods';
	import { LineNumbers, Highlight } from 'svelte-highlight';
	import { typescript } from 'svelte-highlight/languages';
	import { githubDark } from 'svelte-highlight/styles';
	let dropDownVisible = false;
	let activeMethod = 'GET';
	let inputValue = '';
	let code = '';
	let hlRef: Highlight;

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
	{@html githubDark}</svelte:head
>

<main
	class="bg-background grid h-screen grid-cols-[15%_auto] grid-rows-[5%_auto_40%] overflow-hidden"
>
	<!-- Navbar -->
	<div
		class="border-border col-span-2 flex items-center justify-center border-b text-lg font-bold text-white"
	>
		Header
	</div>

	<!-- Sidebar -->
	<div class="border-border col-span-1 row-span-2 border-r p-4 text-white">Sidebar</div>

	<!-- Main Content -->
	<div class="col-span-1 p-4">
		<div class="flex items-center">
			<form class="flex w-full">
				<div class="relative inline-block text-left">
					<button
						type="button"
						onclick={toggleVisible}
						class="border-border text-md inline-flex h-full w-[8rem] items-center justify-between rounded-md rounded-r-none border border-r-0 p-2 px-4 font-semibold {methods[
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
						<div class="bg-foreground absolute left-0 z-10 mt-1 w-40 rounded-md p-2 shadow-lg">
							{#each Object.entries(methods) as [method, color]}
								<button
									class={`${color} w-full cursor-pointer p-2 text-left text-xs font-semibold transition duration-100 hover:bg-white/20 ${method === activeMethod ? 'bg-white/20' : ''}`}
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
					class="border-border text-text-primary focus:border-accent w-full rounded-md rounded-l-none border px-3 focus:{methods[
						activeMethod
					]} focus:outline-none"
					placeholder="URL"
				/>
			</form>
			<button
				onclick={handleRequest}
				class="border-border bg-accent hover:bg-accent/30 mx-2 cursor-pointer rounded-md px-8 py-2 font-semibold text-white transition"
			>
				Send
			</button>
		</div>
	</div>

	<!-- JSON Response Section -->
	<div class="border-border col-span-1 overflow-hidden border-t">
		<div class="flex h-full flex-col items-start justify-center">
			<div>
				<ul class="flex flex-row text-xs">
					<li class="text-text-secondary hover:text-text-primary cursor-pointer p-3 px-6">Body</li>
					<li class="text-text-secondary hover:text-text-primary cursor-pointer p-3 px-6">
						Headers
					</li>
					<li class="text-text-secondary hover:text-text-primary cursor-pointer p-3 px-6">
						Cookies
					</li>
				</ul>
			</div>
			{#if code == ''}
				<p
					class="text-text-primary flex h-full w-full items-center justify-center text-center text-lg"
				>
					Make a request! :)
				</p>
			{:else}
				<Highlight language={typescript} {code} bind:this={hlRef} let:highlighted>
					<LineNumbers
						{highlighted}
						--font-size="12px"
						--border-color="#30363d"
						--padding-left="2em"
						--padding-right="1em"
						wrapLines
						class="ml-6 h-full overflow-y-auto text-xs"
					/>
				</Highlight>
			{/if}
		</div>
	</div>
</main>
