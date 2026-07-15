<script>
	import * as d3 from 'd3';

	import oldData from './311_requests.json';
	import { tick } from 'svelte';

	const parseDate = d3.timeParse("%Y %b %d %I:%M:%S %p")

	const data = oldData.map(d => {
		let count = d.count_unique_key;

		if (typeof count === "string" && count.includes(",")) {
			count = count.replace(",", "");
		}

		return {
			month: parseDate(d.by_month_created_date),
			category: d["Problem (formerly Complaint Type)"],
			count: Number(count)
		};
	});

	const groupedData = [];

	data.forEach(d => {
		let row = groupedData.find(r => r.month.getTime() === d.month.getTime());

		if (!row) {
			row = { month: d.month };
			groupedData.push(row);
		}

		row[d.category] = d.count;
	});

	groupedData.sort((a, b) => a.month - b.month);

	const categories = [...new Set(data.map(d => d.category))];

	const series = d3.stack().keys(categories)(groupedData);

	const width = 928
	const height = 500
	const margin = {
		top: 20,
		right: 30,
		bottom: 30,
		left: 70
	}


  // Declare the x (horizontal position) scale.
	const xScale = d3.scaleBand()
    	.domain(groupedData.map(d => d.month))
    	.range([margin.left, width - margin.right])
		.padding(0.1);

	const xTicks = groupedData.filter((d, i) => i % 6 === 0);

  // Declare the y (vertical position) scale.
	const yScale = d3.scaleLinear()
    	.domain([0, d3.max(groupedData, d => d3.sum(categories, c => d[c] || 0))])
		.nice()
    	.range([height - margin.bottom, margin.top]);
	
	const yTicks = yScale.ticks(10)

	const colorScale = d3.scaleOrdinal()
    	.domain(categories)
    	.range(d3.schemeTableau10);

</script>

<svg viewBox="0 0 928 500" {width} {height}>

	{#each yTicks as yTick}
		<line
			x1={margin.left-10}
			x2={margin.left}
			y1={yScale(yTick)}
			y2={yScale(yTick)}
			stroke="#000"
			stroke-width="1"
		/>

		<line
			x1={margin.left}
			x2={width - margin.right}
			y1={yScale(yTick)}
			y2={yScale(yTick)}
			stroke="#bababa"
			stroke-width="0.5"
		/>


		<text
			x={margin.left-15}
			y={yScale(yTick)}
			text-anchor="end"
			font-size="12"
			font-family="sans-serif"
			dominant-baseline="middle"
		>
			{yTick.toLocaleString()}
		</text>
	{/each}

	{#each xTicks as xTick}
		<text
			x={xScale(xTick.month) + xScale.bandwidth() / 2}
			y={height - margin.bottom + 20}
			text-anchor="middle"
			font-size="12"
		>
			{d3.timeFormat("%b %Y")(xTick.month)}
		</text>

		<line
			x1={xScale(xTick.month)}
			x2={xScale(xTick.month)}
			y1={height - margin.bottom}
			y2={height - margin.bottom + 10}
			stroke="#000"
			stroke-width="1"
		/>
	{/each}
	
		
	{#each series as layer}
        {#each layer as bar}
        	<rect
				x={xScale(bar.data.month)}
				y={yScale(bar[1])}
				width={xScale.bandwidth()}
				height={yScale(bar[0]) - yScale(bar[1])}
				fill={colorScale(layer.key)}
			/>
    	{/each}
	{/each}



</svg>