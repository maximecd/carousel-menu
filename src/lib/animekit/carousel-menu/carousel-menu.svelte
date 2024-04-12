<script lang="ts">
	import CampingImg from './assets/camping.jpg';
	import CyclingImg from './assets/cycling.jpg';
	import RunningImg from './assets/running.jpg';
	import FitnessImg from './assets/fitness.jpg';
	import KayakingImg from './assets/kayaking.jpg';

	import { cn } from '$lib/utils';
	import { ArrowRight } from 'lucide-svelte';
	import type { NavItem } from './types/NavItem';
	import Carousel from './components/carousel.svelte';

	const navItems: NavItem[] = [
		{ id: 1, name: 'Camping', img: CampingImg },
		{ id: 2, name: 'Cycling', img: CyclingImg },
		{ id: 3, name: 'Running', img: RunningImg },
		{ id: 4, name: 'Fitness & Gym', img: FitnessImg },
		{ id: 5, name: 'Kayaking', img: KayakingImg }
	];

	let activeItem = 0;

	let hasInteracted = false;

	function setActiveItem(i: number) {
		if (!hasInteracted) {
			hasInteracted = true;
		}
		activeItem = i;
	}

	let clear: number;
	$: {
		clearInterval(clear);
		if (!hasInteracted) {
			clear = setInterval(() => {
				if (activeItem < navItems.length - 1) {
					activeItem += 1;
				} else {
					activeItem = 0;
				}
			}, 3000);
		}
	}
</script>

<div
	class="grid w-full max-w-[1800px] items-center gap-0 overflow-hidden bg-white p-4 md:grid-cols-6 md:p-16"
>
	<nav class="z-10 col-span-2 mb-14 flex w-full md:mb-0">
		<div class="w-full">
			<h3 class="mb-4 text-sm uppercase">Shop by sport</h3>
			<ul>
				{#each navItems as item, i}
					<li on:pointerenter={() => setActiveItem(i)} class="py-0.5 md:py-4">
						<a
							href="/"
							class="group w-full text-2xl font-semibold hover:text-blue-800 focus:text-blue-800 md:text-3xl xl:text-5xl"
						>
							<span class="inline-flex items-center gap-1">
								{item.name}
								<ArrowRight
									class={cn(
										'h-5 w-5 scale-0 stroke-2 transition-transform duration-500 group-hover:scale-100 group-focus:scale-100 md:h-9 md:w-9 md:stroke-[1.5]',
										activeItem === i ? 'scale-100' : 'scale-0'
									)}
								/>
							</span>
						</a>
					</li>
				{/each}
			</ul>
		</div>
	</nav>
	<Carousel items={navItems} bind:activeItem />
</div>
