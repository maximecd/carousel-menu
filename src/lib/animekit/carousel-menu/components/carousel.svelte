<script lang="ts">
	import { gsap } from 'gsap';

	import type { NavItem } from '../types/NavItem';
	import { onMount } from 'svelte';

	export let items: NavItem[];

	let slides: HTMLElement[] = [];

	onMount(() => {
		isMounted = true;
	});

	$: orderedSlides = slides.slice(activeItem).concat(slides.slice(0, activeItem));

	let isMounted = false;

	export let activeItem: number;

	$: orderedItems = items.slice(activeItem).concat(items.slice(0, activeItem));

	$: if (isMounted && activeItem !== null) {
		animateSlides();
	}

	function animateSlides() {
		const tl = gsap.timeline();

		let duration = 0.5;
		let stagger = 0.03;

		tl.to(orderedSlides[orderedSlides.length - 1], {
			xPercent: -40,
			yPercent: 20,
			duration,
			opacity: 0,
			scale: 0.7
		});

		tl.to(
			orderedSlides.slice(0, -1),
			{
				xPercent: (i) => i * 50,
				yPercent: (i) => i * -5,
				duration,
				opacity: (i) => (i > 2 ? 0 : 1),
				display: (i) => (i > 2 ? 'none' : 'block'),
				scale: 1,
				stagger
			},
			`<=${stagger}`
		);
	}

	let mouseDown = {
		x: 0,
		y: 0
	};

	function handleMouseDown(e: MouseEvent) {
		mouseDown = {
			x: e.clientX,
			y: e.clientY
		};
	}

	function handleMouseUp(e: MouseEvent) {
		const deltaX = e.clientX - mouseDown.x;

		if (Math.abs(deltaX) > 100) {
			if (deltaX > 0) {
				// swipe right
				activeItem = activeItem === 0 ? items.length - 1 : activeItem - 1;
			} else {
				// swipe left
				activeItem = activeItem === items.length - 1 ? 0 : activeItem + 1;
			}
		}
	}

	function handleKeyDown(e: KeyboardEvent) {
		if (e.key === 'ArrowRight') {
			activeItem = activeItem === items.length - 1 ? 0 : activeItem + 1;
		} else if (e.key === 'ArrowLeft') {
			activeItem = activeItem === 0 ? items.length - 1 : activeItem - 1;
		}
	}
</script>

<div class="col-span-3 aspect-square">
	<div
		class="relative h-full"
		role="listbox"
		tabindex="0"
		on:pointerdown={(e) => handleMouseDown(e)}
		on:pointerup={(e) => handleMouseUp(e)}
		on:keydown={(e) => handleKeyDown(e)}
	>
		{#each items as item, i}
			<div
				class="absolute h-full"
				bind:this={slides[i]}
				style="
          z-index: {orderedItems.toReversed().findIndex((o) => o.id === item.id)};
        "
			>
				<img
					src={item.img}
					alt={item.name}
					class="h-full w-full select-none object-cover"
					draggable="false"
				/>
			</div>
		{/each}
	</div>
</div>

<style>
</style>
