<script>
	import * as d3 from 'd3';

	import oldData from './aapl.json';

	const data = oldData.map(d => ({...d, date: new Date(d.date)}));

	const width = 928
	const height = 500
	const margin = {
		top: 20,
		right: 30,
		bottom: 30,
		left: 40
	}

	// defining maxClose so that I can reference it later (just cleaning up the code a little)
	const maxClose = d3.max(data, d => d.close);

  // Declare the x (horizontal position) scale.
	const xScale = d3.scaleUtc(d3.extent(data, d => d.date),
						[margin.left, width - margin.right]);

  // Declare the y (vertical position) scale.
	const yScale = d3.scaleLinear([0, maxClose],
						[height - margin.bottom, margin.top]);

  // Declare the line generator.
	const lineGenerator = d3.line()
		.x(d => xScale(d.date))
		.y(d => yScale(d.close));

  // defined this with the help of chatGPT to responsively return ticks at intervals of $50
	const yTicks = d3.range(0, Math.floor(maxClose / 50) * 50 + 1, 50);

  // working on the logic to define my x-axis ticks with the pattern "2008 April July October 2009 April July October", etc.
	const startDate = d3.min(data, d => d.date);
	const endDate = d3.max(data, d => d.date);

  // was helped in writing this line by chatGPT also, though I understand all the logic of it
	const xTicks = d3.timeMonth.every(3).range(d3.timeMonth.ceil(startDate), endDate);
  
  // this line also came from chatGPT but I similarly understand the logic behind it
	const formatMonth = d => {
		if (d.getMonth() === 0) {
			return d.getFullYear();
		}
		
		return d3.timeFormat("%B")(d);};


</script>

<svg viewBox="0 0 928 500" width="928" height="500">
	
	<!-- y-axis -->
	{#each yTicks as yTick}
  	
		<!-- axis labels -->
		<text
    		x={margin.left - 10}
    		y={yScale(yTick)}
    		text-anchor="end"
    		alignment-baseline="middle"
			font-size=10
			font-family="sans-serif"
  		>
    	{yTick}
  		</text>

		<!-- short ticks -->
		<line
			x1={margin.left-5}
			x2={margin.left}
			y1={yScale(yTick)}
			y2={yScale(yTick)}
			stroke="#000000"
			stroke-width="1"
		/>

		<!-- longer lines -->
		<line
			x1={margin.left}
			x2={width - margin.right}
			y1={yScale(yTick)}
			y2={yScale(yTick)}
			stroke="#bababa"
			stroke-width="0.5"
		/>
	{/each}

	{#each xTicks as xTick}
		<text
    		x={xScale(xTick)}
    		y={height - margin.bottom + 15}
    		text-anchor="middle"
    		alignment-baseline="top"
			font-size=10
			font-family="sans-serif"
  		>
    	{formatMonth(xTick)}
  		</text>
		
		<line
			x1={xScale(xTick)}
			x2={xScale(xTick)}
			y1={height - margin.bottom}
			y2={height - margin.bottom + 5}
			stroke="#000000"
			stroke-width="1"
		/>
	{/each}

	<!-- x-axis -->
	<line 
		x1={margin.left}
		x2={width - margin.right}
		y1={height - margin.bottom}
		y2={height - margin.bottom}
		stroke="#000"
		stroke-width=1
	/>

	<!-- the line itself -->
	<path d={lineGenerator(data)} fill="none" stroke = "steelblue" stroke-width="2" />

</svg>

										<!-- // Create the SVG container.
										const svg = d3.create("svg")
											.attr("width", width)
											.attr("height", height)
											.attr("viewBox", [0, 0, width, height])
											.attr("style", "max-width: 100%; height: auto; height: intrinsic;");

										// Add the x-axis.
										svg.append("g")
											.attr("transform", `translate(0,${height - marginBottom})`)
											.call(d3.axisBottom(x).ticks(width / 80).tickSizeOuter(0));

										// Add the y-axis, remove the domain line, add grid lines and a label.
										svg.append("g")
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
												.text("↑ Daily close ($)"));

										// Append a path for the line.
										svg.append("path")
											.attr("fill", "none")
											.attr("stroke", "steelblue")
											.attr("stroke-width", 1.5)
											.attr("d", line(aapl));

										return svg.node();
										}

</script>

<svg viewBox="0 0 {width} {height}" {width} {height}>
	
</svg> -->
