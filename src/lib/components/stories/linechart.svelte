<script>
	// //
	// chart = {
	//   // Declare the chart dimensions and margins.
	//   const width = 928;
	//   const height = 500;
	//   const marginTop = 20;
	//   const marginRight = 30;
	//   const marginBottom = 30;
	//   const marginLeft = 40;

	//   // Declare the x (horizontal position) scale.
	//   const x = d3.scaleUtc(d3.extent(aapl, d => d.date), [marginLeft, width - marginRight]);

	//   // Declare the y (vertical position) scale.
	//   const y = d3.scaleLinear([0, d3.max(aapl, d => d.close)], [height - marginBottom, marginTop]);

	//   // Declare the line generator.
	//   const line = d3.line()
	//       .defined(d => !isNaN(d.close))
	//       .x(d => x(d.date))
	//       .y(d => y(d.close));

	//   // Create the SVG container.
	//   const svg = d3.create("svg")
	//       .attr("width", width)
	//       .attr("height", height)
	//       .attr("viewBox", [0, 0, width, height])
	//       .attr("style", "max-width: 100%; height: auto; height: intrinsic;");

	//   // Add the x-axis.
	//   svg.append("g")
	//       .attr("transform", `translate(0,${height - marginBottom})`)
	//       .call(d3.axisBottom(x).ticks(width / 80).tickSizeOuter(0));

	//   // Add the y-axis, remove the domain line, add grid lines and a label.
	//   svg.append("g")
	//       .attr("transform", `translate(${marginLeft},0)`)
	//       .call(d3.axisLeft(y).ticks(height / 40))
	//       .call(g => g.select(".domain").remove())
	//       .call(g => g.selectAll(".tick line").clone()
	//           .attr("x2", width - marginLeft - marginRight)
	//           .attr("stroke-opacity", 0.1))
	//       .call(g => g.append("text")
	//           .attr("x", -marginLeft)
	//           .attr("y", 10)
	//           .attr("fill", "currentColor")
	//           .attr("text-anchor", "start")
	//           .text("↑ Daily close ($)"));

	//   // Append a path for the line.
	//   svg.append("path")
	//       .attr("fill", "none")
	//       .attr("stroke", "#ccc")
	//       .attr("stroke-width", 1.5)
	//       .attr("d", line(aaplMissing.filter(d => !isNaN(d.close))));

	//   // Append a path for the line.
	//   svg.append("path")
	//       .attr("fill", "none")
	//       .attr("stroke", "steelblue")
	//       .attr("stroke-width", 1.5)
	//       .attr("d", line(aaplMissing));

	//   return svg.node();
	// } -->

	// <script>
	import * as d3 from 'd3';

	import data from './aapl.json';

	// const data = [
	// 	{ month: 'Jan', temp: 40 },
	// 	{ month: 'Feb', temp: 44 },
	// 	{ month: 'Mar', temp: 54 },
	// 	{ month: 'Apr', temp: 65 },
	// 	{ month: 'May', temp: 75 },
	// 	{ month: 'Jun', temp: 83 },
	// 	{ month: 'Jul', temp: 88 },
	// 	{ month: 'Aug', temp: 86 },
	// 	{ month: 'Sep', temp: 78 },
	// 	{ month: 'Oct', temp: 67 },
	// 	{ month: 'Nov', temp: 55 },
	// 	{ month: 'Dec', temp: 44 }
	// ];

	const width = 928;
	const height = 500;
	const margin = { top: 20, right: 30, bottom: 30, left: 40 };

	const xScale = d3
		.scalePoint()
		.domain(data.map((d) => d.date))
		.range([margin.left, width - margin.right]);

	const yScale = d3
		.scaleLinear()
		.domain([0, d3.max(data, (d) => d.close)])
		.range([height - margin.bottom, margin.top]);

	const lineGenerator = d3
		.line()
		.x((d) => xScale(d.date))
		.y((d) => yScale(d.close));

	const areaGenerator = d3
		.area()
		.x((d) => xScale(d.date))
		.y0(height - margin.bottom)
		.y1((d) => yScale(d.close));

	// to define
	function xAxis(node, scale) {
		const axis = d3
			.axisBottom(scale)
			.ticks(d3.timeMonth.every(1)) // Forces exactly one tick per month
			.tickFormat(d3.timeFormat('%b')); // Formats as "Jan", "Feb", "Mar"

		d3.select(node).call(axis);

		return {
			update(newScale) {
				d3.select(node).call(axis.scale(newScale));
			}
		};
	}
</script>

<svg viewBox="0 0 {width} {height}" {width} {height}>
	{#each yScale.ticks(13) as tick}
		<line
			x1={margin.left}
			y1={yScale(tick)}
			x2={width - margin.right}
			y2={yScale(tick)}
			stroke="#e5e5e5e5"
		/>

		<line
			x1={margin.left - 6}
			y1={yScale(tick)}
			x2={margin.left}
			y2={yScale(tick)}
			stroke="#000000"
		/>

		<text
			x={margin.left - 10}
			y={yScale(tick)}
			text-anchor="end"
			dominant-baseline="middle"
			font-size="12"
			fill="#666"
		>
			{tick}
		</text>
	{/each}

	{#each data as d}
		<!-- <line 
			x1={xScale(d.date)}
			y1={margin.top}
			x2={xScale(d.date)}
			y2={height-margin.bottom}
			stroke="#ffffff"
		/> -->

		<!-- <text
			x={xScale(d.date)}
			y={height - margin.bottom + 5}
			dominant-baseline="hanging"
			text-anchor="middle"
			font-size="14"
			fill="#666"
		>
			{d.date}
		</text> -->
	{/each}

	<line
		x1={margin.left}
		x2={width - margin.right}
		y1={height - margin.bottom}
		y2={height - margin.bottom}
		stroke="#000"
	/>

	<!-- {#each data as d} -->

	<!-- svg.append("g")
      .attr("transform", `translate(${marginLeft},0)`)
      .call(d3.axisLeft(y).ticks(height / 40))
      .call(g => g.select(".domain").remove())
      .call(g => g.selectAll(".tick line").clone()
          .attr("x2", width - marginLeft - marginRight)
          .attr("stroke-opacity", 0.1))
      .call(g => g.append("text")
          .attr("x", -marginLeft)
          .attr("y", 10)
          .attr("fill", "currentColor")
          .attr("text-anchor", "start")
          .text("↑ Daily close ($)")); -->

	<!-- <line
		x1={margin.left}
		x2={margin.left}
		y1={margin.top}
		y2={height - margin.bottom}
		stroke="#000"
	/> -->

	<!-- <path
		d={areaGenerator(data)}
		fill="steelblue"
		opacity="0.15"
	/> -->

	<path d={lineGenerator(data)} fill="none" stroke="steelblue" stroke-width="1" />

	<!-- {#each data as d}
		<circle
			cx={xScale(d.date)}
			cy={yScale(d.close)}
			r="4"
			fill="white"
			stroke="steelblue"
			stroke-width="2"
		/>

	{/each} -->
</svg>
