<script>
	import { pie, arc } from 'd3-shape';
	import data from '../data/data.json';
	import cols from '../data/teamcols.json';

	let height = 700;
	let width = height;
	let outer = height / 4;
	let inner = height / 5.5;

	const arcGenerator = arc().innerRadius(inner).outerRadius(outer);

	const pieGenerator = pie().value(1);
	const pieData = pieGenerator(data);

	// Function to calculate label positions
	function calculateLabelPosition(slice) {
		const midAngle = (slice.startAngle + slice.endAngle) / 2;
		const labelRadius = outer + (height/50); // Adjust the label radius as needed
		const labelX = labelRadius * Math.cos(midAngle - Math.PI / 2);
		const labelY = labelRadius * Math.sin(midAngle - Math.PI / 2);
		return [labelX, labelY];
	}

	// Dynamic transform calculation
	let centerX = width / 2;
	let centerY = height / 2;
	$: transform = `translate(${centerX}, ${centerY})`;
</script>

<svg {width} {height}>
	<g {transform}>
		{#each pieData as slice, i}
			<path
				d={arcGenerator(slice)}
				fill={cols[slice.data.constructor.split('-')[0]]}
				stroke="black"
				stroke-width="0.25"
				key={i}
			/>
			<g
				transform="translate({calculateLabelPosition(slice)[0]}, {calculateLabelPosition(
					slice
				)[1]})"
			>
				<text
					text-anchor={slice.index < pieData.length / 2 ? 'start' : 'end'}
					fill="black"
					dominant-baseline="middle"
					font-size={height/100}
					transform={slice.index > pieData.length / 2
						? `rotate(${slice.startAngle * (180 / Math.PI) + 90})`
						: `rotate(${slice.startAngle * (180 / Math.PI) - 90})`}
				>
					{slice.index > pieData.length / 2
						? `${slice.data.constructor} - ${slice.data.season}`
						: `${slice.data.season} - ${slice.data.constructor}`}
				</text>
			</g>
		{/each}
	</g>
</svg>

<style>
	svg {
		background-color: #f0f0f0;
		border-radius: 5px;
	}
</style>
