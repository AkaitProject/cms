<script>
	import { setContext } from 'svelte'
	import { loadIcons, enableCache } from '@iconify/svelte'
	import '$lib/assets/reset.css'
	import { browser } from '$app/environment'
	import { onMount } from 'svelte'
	import { registerProcessors } from '@worlddev/cms-builder'
	import Modal from '$lib/components/Modal.svelte'
	import { invalidate } from '$app/navigation'
	import supabase_client from '$lib/supabase'

	export let data

	let { supabase, session } = data
	$: ({ supabase, session } = data)

	$: session &&
		supabase_client.auth.setSession({
			access_token: session.access_token,
			refresh_token: session.refresh_token
		})

	onMount(() => {
		if (!supabase) return
		const {
			data: { subscription }
		} = supabase.auth.onAuthStateChange((event, _session) => {
			if (_session?.expires_at !== session?.expires_at) {
				invalidate('supabase:auth')
			}
		})
		return () => subscription.unsubscribe()
	})

	if (browser) {
		import('../compiler/processors.js').then(({ html, css }) => {
			registerProcessors({ html, css })
		})
		setContext('track', () => {})
		setContext('DEBUGGING', new URLSearchParams(window.location.search).has('debug'))
	}

	loadIcons([
		'gg:spinner',
		'material-symbols:edit-square-outline-rounded',
		'pepicons-pop:trash',
		'gridicons:external'
	])
	enableCache('local')
</script>

<Modal />
<slot />

<style global lang="postcss">
	@import url(https://fonts.bunny.net/css?family=inter:300,400,500,600,700,800);

	.wdt-reset {
		@tailwind base;
		font-family: 'Inter', serif !important;
		direction: ltr;

		/* height: 100vh; */
		/* overflow: hidden; */

		--wdt-color-brand: #efefef;
		--wdt-color-brand-dark: #656565;
		--wdt-color-white: white;
		--wdt-color-codeblack: rgb(30, 30, 30);
		--wdt-color-codeblack-opaque: rgba(30, 30, 30, 0.9);

		--wdt-color-black: rgb(17, 17, 17);
		--wdt-color-black-opaque: rgba(17, 17, 17, 0.95);

		--color-gray-1: rgb(245, 245, 245);
		--color-gray-2: rgb(229, 229, 229);
		--color-gray-3: rgb(212, 212, 212);
		--color-gray-4: rgb(156, 163, 175);
		--color-gray-5: rgb(115, 115, 115);
		--color-gray-6: rgb(82, 82, 82);
		--color-gray-7: rgb(64, 64, 64);
		--color-gray-8: rgb(38, 38, 38);
		--color-gray-9: rgb(23, 23, 23);

		--font-size-1: 0.75rem;
		--font-size-2: 0.875rem;
		--font-size-3: 1.125rem;
		--font-size-4: 1.25rem;

		--box-shadow-xl: 0 0 #0000, 0 0 #0000, 0 20px 25px -5px rgba(0, 0, 0, 0.1),
			0 10px 10px -5px rgba(0, 0, 0, 0.04);

		--transition-colors: background-color 0.1s, border-color 0.1s, color 0.1s, fill 0.1s,
			stroke 0.1s;

		--padding-container: 15px;
		--max-width-container: 1900px;

		--ring: 0px 0px 0px 2px var(--wdt-color-brand);

		--wdt-max-width-1: 30rem;
		--wdt-max-width-2: 1200px;
		--wdt-max-width-max: 1200px;

		--wdt-border-radius: 5px;
		--wdt-ring-brand: 0px 0px 0px 1px var(--wdt-color-brand);
		--wdt-ring-brand-thin: 0px 0px 0px 1px var(--wdt-color-brand);
		--wdt-ring-brand-thick: 0px 0px 0px 3px var(--wdt-color-brand);
	}

	button,
	a {
		cursor: pointer;
	}

	body {
		margin: 0;
	}

	.wdt-input {
		appearance: none;
		border: 0;
		background-color: transparent;
		font-size: inherit;
		background: var(--color-white);
		padding: 0.5rem 0.75rem;
		width: 100%;

		/* &:focus {
  box-shadow: 0 0 0 1px var(--color-wdtred);
  border: 0;
}

&:placeholder {
  color: var(--color-gray-5);
} */
	}

	.wdt-modal {
		color: var(--color-gray-1);
		/* background: var(--color-gray-9); */
		padding: 1.5rem;
		border-radius: var(--wdt-border-radius);
		margin: 0 auto;
		/* width: 100vw; */
	}

	.wdt-heading-xl {
		margin-bottom: 0.5rem;
		font-size: 1.25rem;
		line-height: 1.75rem;
		font-weight: 700;
	}

	.wdt-heading-lg {
		margin-bottom: 0.25rem;
		font-size: 1.1rem;
		line-height: 1.5rem;
		font-weight: 700;
	}

	.sr-only {
		position: absolute;
		width: 1px;
		height: 1px;
		padding: 0;
		margin: -1px;
		overflow: hidden;
		clip: rect(0, 0, 0, 0);
		white-space: nowrap;
		border-width: 0;
	}
</style>
