<script>
	import { createEventDispatcher } from 'svelte'
	import { fade } from 'svelte/transition'
	import Icon from '@iconify/svelte'
	import Spinner from '../../components/misc/Spinner.svelte'
	import { showKeyHint } from '../../stores/app/misc'

	const dispatch = createEventDispatcher()

	export let id = null
	export let title = ''

	/** @type {string | null} */
	export let label = null
	export let key = null
	export let icon = null
	export let svg = null
	export let disabled = false
	export let onclick = null
	export let loading = false
	export let active = false
	export let buttons = null
	export let type = null
	export let style = ''

	let subButtonsActive = false
</script>

<button
	{id}
	aria-label={title}
	class="wdt-button"
	class:wdt={type === 'wdt'}
	class:active
	class:has-subbuttons={buttons}
	class:has-icon-button={!label && icon}
	{style}
	in:fade={{ duration: 200 }}
	{disabled}
	on:click={() => {
		subButtonsActive = !subButtonsActive
		onclick ? onclick() : dispatch('click')
	}}
>
	{#if icon || svg}
		{#if key}
			<span class="key-hint" class:active={$showKeyHint} aria-hidden>
				&#8984;{key.toUpperCase()}
			</span>
		{/if}
		{#if loading}
			<Spinner />
		{:else if label && svg}
			<div class="svg">
				{@html svg}
			</div>
			<span class="label">{label}</span>
		{:else if label && icon}
			<Icon {icon} />
			<span class="label">{label}</span>
		{:else if svg}
			<div class="svg">
				{@html svg}
			</div>
		{:else if icon}
			<Icon {icon} />
		{/if}
	{:else}
		<slot>
			<span>{label}</span>
		</slot>
	{/if}
</button>

<style lang="postcss">
	@tailwind base;

	.wdt-button {
		font-size: 0.85rem;
		user-select: none;
		border-radius: 0;

		--Spinner-size: 0.75rem;

		/* &:first-child {
      border-top-left-radius: var(--wdt-border-radius);
      border-bottom-left-radius: var(--wdt-border-radius);
    }

    &:last-child {
      border-top-right-radius: var(--wdt-border-radius);
      border-bottom-right-radius: var(--wdt-border-radius);
    } */

		&[disabled] {
			background: none;
			opacity: 0.35;
			pointer-events: none;
		}
	}

	.wdt-button.wdt {
		padding: 7px 14px;
		color: var(--wdt-color-white);
		border: 1.5px solid var(--wdt-color-brand);
		border-radius: 0.25rem;
	}

	.wdt-button {
		height: 100%;
		color: var(--wdt-color-white);
		font-weight: 400;
		font-size: 13px;
		padding: 10px 12px;
		transition: 0.1s box-shadow;
		outline: 0;
		position: relative;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;

		&.has-icon-button {
			padding: 8px;

			:global(svg) {
				width: 25px;
				height: auto;
			}
		}
		&:first-child {
			border-top-left-radius: var(--wdt-border-radius);
			border-bottom-left-radius: var(--wdt-border-radius);
		}

		&:last-child {
			border-top-right-radius: var(--wdt-border-radius);
			border-bottom-right-radius: var(--wdt-border-radius);
		}

		&:hover,
		&:focus {
			/* background: var(--wdt-color-codeblack); */
			background: #1f1f1f;
			/* z-index: 2; */
		}

		&:active {
			background: #404040;
			/* box-shadow: var(--wdt-ring-wdtgreen); */
			/* background: var(--wdt-color-brand); */
			/* color: var(--color-gray-8); */
		}
	}

	.wdt-button[disabled] {
		opacity: 0.1;
		cursor: default;
		transition: var(--transition-colors);

		&:hover,
		&:focus {
			box-shadow: none;
		}
	}

	@keyframes spin {
		from {
			transform: rotate(0deg);
		}
		to {
			transform: rotate(360deg);
		}
	}
</style>
