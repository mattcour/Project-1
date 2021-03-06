<!DOCTYPE html>
<html>
<head>
    <title>1854, London: Cholera Outbreak Visualization Draft</title>
    <script src="https://d3js.org/d3.v3.min.js"></script>
	<style>
    	text { 
			font-family: Arial; 
			font-size: 12px;
		}
		.axis path, .axis line {
			fill: none;
			stroke: black;
			shape-rendering: crispEdges;
		}

        .axis text {
				font-family: sans-serif;
				font-size: 11px;
			}
		.tick text {
			fill: black;
			font-size: 11px;
		}

		rect {
			stroke: white;
		}			

	</style>
</head>
<body> 

	<svg id="main" width="2400" height="2700">
    
      <g transform="translate(0,0)" id="map" ></g>
      <g transform="translate(790,45)" id="timechart" ></g>
      <g transform="translate(100, 850)" id="explanation" ></g>
      <g transform="translate(740, 800)" id="agechart" ></g>
  </svg>
  
	<script>    
// define some variables upfront, i.e. globally
 
    var padding = 30;
    // this variable to be used in time chart, potentially
    var margin = {top: 50, right: 20, bottom: 70, left: 40},
        width = 900 - margin.left - margin.right,
        height = 800 - margin.top - margin.bottom;
    
// lets get some scales
  // scales for map		
    var xScale = d3.scale.linear();
    var yScale = d3.scale.linear();
//setting domain/range for map scales      
      xScale.domain([0,20]).range([0,700]);
      yScale.domain([20,0]).range([0,700]);

// scales for time chart
    var x = d3.scale.ordinal()
            .rangeRoundBands([0, width]);
    var y = d3.scale.linear()
            .domain([0,155]).range([height, 0]);

//scales for scatterplot of deaths and age
    var xScale3 = d3.scale.linear()
            .domain([0,5])
            .range([0, width]);
    var yScale3 = d3.scale.linear()
            .domain([0,155])
            .range([height,0]); 

//scales for barchart of deaths and gender
    var xScale4 = d3.scale.linear().domain([0,1]).range([0, width]);
    var yScale4 = d3.scale.linear().domain([0,575]).range([height,0]); 

//loading map data        
	d3.json('streets.json', function(json) {
        /// checking to see it loaded
        console.log(json);
        /// success! now let's make some lines      
        var	lineFunction = d3.svg.line()
	        .x(function(d) { return xScale(d.x); })
            .y(function(d) { return yScale(d.y); });
          
            d3.select("#map"). selectAll("path")
                .data(json)
                .enter()
                .append("path")
                /// arbitrarly named it block, thinking city blocks
                .attr("d", function(block){
                     return lineFunction(block) })
                .attr('fill', 'none')
                .attr("stroke", 'black')
         });

/// now that the map is in, let's add the DEATH AGE SEX data
    d3.csv('deaths_age_sex.csv', function(deaths){
        console.log(deaths); 
        //and it is in! now time to map these coordinates. 
        var svg = d3.select("#map")
        // making circles for death locations
            svg.selectAll("circle")
                .data(deaths) 
                .enter()
                .append("circle")
                .attr("class", "victim")
                .attr("cx", function(d) {return xScale(d.x)})
                .attr("cy", function(d) {return yScale(d.y)})
                .attr("r", 2.5)
                .attr("fill", "#b2df8a") // using color blind safe colorscheme from colorbrewer
                .attr('id', function(d, i) {return 'left_' + (i+1)}); //making IDs to link datasets/visuals

//adding labels
            svg.append('text')    
                .attr('x', 400)
                .attr('y', 30)
     		    .attr('text-anchor', 'middle')
                .style('font-weight', 'bold')
                .style('font-size', '24px')
                .html('Tracking the Cholera Outbreak in London'); 

            //oxford street
            svg.append("text")
                .attr("x", 328)
                .attr("y", 191)
                .attr("transform", "rotate(-12)")
                .text("Oxford Street")
                .attr("fill", "black")
                .style("font-size", "11px")
               // success!!!

            //work house
            svg.append("text")
                .attr("x", 280)
                .attr("y", 300)
                .attr("transform", "rotate(-12)")
                .text("Work House")
                .attr("fill", "black")

            // brewery
            svg.append("text")
                .attr("x", 446)
                .attr("y",-316)
                .attr("transform", "rotate(65)")  
                .text("Brewery")
                .attr("fill", "black")
                .style("font-size", "9px")
               
            // regent street    
            svg.append("text")
                .attr("x", 395)
                .attr("y", -100)
                .attr("transform", "rotate(63)") 
                .text("Regent Street")
                .attr("fill", "black")

            // song square   (two parts)
            svg.append("text")
                .attr("x", 617)
                .attr("y", 140)
                .text("Song")
                .attr("fill", "black")
            svg.append("text")
                .attr("x", 615)
                .attr("y", 152)
                .text("Square")
                .attr("fill", "black")

            // dean  street 
            svg.append("text")
                .attr("x", 425)
                .attr("y", -445)
                .attr("transform", "rotate(63)") 
                .text("Dean Street")
                .attr("fill", "black")

            // Legend
            svg.append("text")
                .attr("x", 170)
                .attr("y", 610)
                .text("Legend:")
                .attr("fill", "black")
                .style('font-weight', 'bold')
                .style('font-size', '16px')
            svg.append("text")
                .attr("x", 175)
                .attr("y", 630)
                .text("Dots represent location of deaths from Cholera (Total = 571)")
                .attr("fill", "black")
                .style('font-size', '14px')
            svg.append("text")
                .attr("x", 175)
                .attr("y", 650)
                .text("Blue Rectangles represent water pump locations (Total = 13)")
                .attr("fill", "black")
                .style('font-size', '14px')

            // scroll over instructions
            svg.append("text")
                .attr("x", 1120)
                .attr("y", 200)
                .text("Scrolling over the bar chart will highlight deaths on the map to the left")
                .attr("fill", "black")
                .style('font-size', '16px')
            svg.append("text")
                .attr("x", 1120)
                .attr("y", 220)
                .text("as they occurred up to the day reflected in this bar chart. ")
                .attr("fill", "black")  
                .style('font-size', '16px')     
          
// adding pumps data     
     d3.csv('pumps.csv', function(pumps){
        console.log(pumps);     
        //lets add those pumps!!
        var pumpselection = d3.select("#map")
            pumpselection.selectAll("rect")
               .data(pumps)
               .enter()
               .append("rect")
               .attr("x", function(d) {return xScale(d.x)})
               .attr("y", function(d) {return yScale(d.y)})
               .attr("width", 6)
               .attr("height", 8)
               .attr("fill", '#1f78b4') //using colorblind safe color
    });
/*color blind safe color scheme ['#a6cee3' light blue,'#1f78b4','#b2df8a' light green] from ColorBrewer*/
//adding death days data
    d3.csv('deathdays.csv', function(days){
        console.log(days);   });
        
    });
       
// loading days data in a way to make the chart show up
    d3.csv("deathdays.csv", function(error, data) {

        data.forEach(function(d) {
          d.date = d.date;
          d.value = d.deaths;
        });
    
// setting axes variables
    var xAxis = d3.svg.axis()
        .scale(x)
        .orient("bottom")
        .tickFormat(d3.time.format("%Y-%m"))
    /*.svg.append("g") */
    /*    .call(d3.axisbottom(x))
        .selectAll("text")
            .attr("transform", "translate (-10,0) rotate(-45))")
            .style("text-anchor", "end");

       /* .attr("transform", "rotate(90)") */

    var yAxis = d3.svg.axis()
        .scale(y)
        .orient("left")
        .ticks(9);

// further defining scales with deathdays data
    x.domain(data.map(function(d) { return d.date; }))
    /*x.range("0,5"); */
 //   y.domain([0, d3.max(data, function(d) { return d.value; })]); // taking away for now, was overriding earlier command
       
// prep SVG space
    var svg = d3.select("body").append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", 
          "translate(" + margin.left + "," + margin.top + ")");

// adding the bars for the chart
    var svg = d3.select("#timechart")

        svg.selectAll("bar")
        .data(data)
        .enter().append("rect")
        .style("fill", "slateblue")
        .attr("x", function(d) { return x(d.date); })
        .attr("width", x.rangeBand())
        .attr("y", function(d) { return y(d.value); })
        .attr("height", function(d) { return height - y(d.value); })
        .attr('id2', function(d, i) {return 'left_' + (i+1)})

// creating the interactivity between visualizations
        .on("mouseover", function(d, i) {
             console.log(d.accumulative)
             
             d3.selectAll("circle.victim") //only selecting circles labelled as victims above
                .filter(function(d2, i2) {
                     i2 <= (d.accumulative)
                     return i2 <= (d.accumulative)
                })
                .attr("fill", "black") // using 'color blind safe' color scheme from colorbrewer

        })

        .on("mouseout", function(d, i) {
              d3.selectAll("rect.pt" + i)
                  .attr("fill", "slateblue") 

              d3.selectAll("circle.victim") //only selecting circles labelled as victims above
                .filter(function(d2, i2) {
                     i2 <= (d.accumulative)
                     return i2 <= (d.accumulative)
                })
                .attr("fill", "#b2df8a") // makes it change back to original color when mouse scrolls out
        });         
 
// orienting x-axis 
    svg.append("g") // changed '#timechart' to 'g' because of unregonizable code
            .attr('class', 'axis')
            .attr('transform', 'translate(0, '+ height+')')
			.call(d3.svg.axis().scale(x).orient('bottom'))
// orienting y-axis
	svg.append('g')
			.attr('class', 'axis')
			.call(d3.svg.axis().scale(y).orient('left'))

// create the labels for axes
	svg.append('text')
            .attr('x', 440)
            .attr('y', 720)
			.attr('text-anchor', 'middle')
            .style('font-weight', 'bold')
            .style("font-size", "16px")
			.html('Date');

    svg.append('text')    
            .attr('x', -320)
            .attr('y', -30)
            .attr("transform", "rotate(-90)" )
            .attr('text-anchor', 'middle')
            .style("font-size", "16px")
            .style('font-weight', 'bold')
            .html('Number of Deaths'); 

     svg.append('text')    
            .attr('x', 390)
            .attr('y', -10)
     		.attr('text-anchor', 'middle')
            .style('font-weight', 'bold')
            .style('font-size', '24px')
            .html('Cholera Deaths Per Day From August 19th to September 29th'); 

           

//Create X axis
    svg.append("g")
			.attr("class", "axis")
			.attr("transform", "translate(0, height)")
            .call(xAxis);
                
//Create Y axis
	svg.append("g")
        .attr("class", "axis")
        .attr("transform", "translate(" + padding + ",0)")
        .call(yAxis);
       
    }); 

// Adding Text Description of Project
 var svg = d3.select("#explanation")
    svg.append("text")
                .attr("x", 0)
                .attr("y", 0)
                .text("About this Project")
                .attr("fill", "black")
                .style("font-size", "20px")
                .style("font-weight", "bold")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 30)
                .text("The visualizations above depicts an outbreak of cholera in London, circa 1854. The map is of a neighborhood where")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 50)
                .text(" many people became afflicted with the disease and died. The graph on the right displays deaths as they occured over time.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 75)
                .text("Please scroll over the bars on the time chart with your mouse and you should see the locations of those deaths get highlighted on the map.")
                .attr("fill", "black")
                .style("font-size", "14px")

     svg.append("text")
                .attr("x", 0)
                .attr("y", 110)
                .text("Design Process")
                .attr("fill", "black")
                .style("font-size", "16px")
                .style("font-weight", "bold")
            
     svg.append("text")
                .attr("x", 0)
                .attr("y", 140)
                .text("In building this visualization, I was considering various charts to display the breadth of data between the datasets that were made available. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 160)
                .text("The data included coordinates to create a map, locations of where deaths occurred, age and sex of each victim, the locations of nearby water pumps, and also a day by day count of total deaths from Cholera in a 42 day period.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 180)
                .text("John Snow had most of this information when he created a map to visualize where deaths were occurring during the Cholera outbreak of 1854.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 200)
                .text("Through his juxtaposition of death locations on top of a map of a London neighborhood, he was able to see how a great number of these deaths were aggregated around a particular water pump.")
                .attr("fill", "black")
                .style("font-size", "14px")
                
    svg.append("text")
                .attr("x", 0)
                .attr("y", 220)
                .text("As it turns out, that water pump was contaminated, and it was not until putting the pieces together with the aid of this visual that the cause of the outbreak was discovered.")
                .attr("fill", "black")
                .style("font-size", "14px")
               
    svg.append("text")
                .attr("x", 0)
                .attr("y", 240)
                .text("This was a foundational moment for the fields of public health and data visualization.")
                .attr("fill", "black")
                .style("font-size", "14px")
        
    svg.append("text")
                .attr("x", 0)
                .attr("y", 270)
                .text("So I knew I wanted to re-create a map similar to what John Snow made.")
                .attr("fill", "black")
                .style("font-size", "14px")
               
    svg.append("text")
                .attr("x", 0)
                .attr("y", 290)
                .text("I also knew that I wanted to create a separate chart depicting deaths over time, in hope that this could interact with the map to enable the user to see the progression of deaths over time.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 310)
                .text("I had a slew of ideas to try with various line-charts, histograms, stacked bars, and scatterplots but unfortunately I am just a beginner with learning code and all of this had to be made in D3. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 330)
                .text("So stay tuned! I plan to add more visuals to explore this data space.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 360)
                .text("I had drafted some ideas in Excel to get a feel for what I might see for the quantitative data. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 380)
                .text("It was helpful to get a gauge of what my time chart should approximate and also helped me identify that some visuals I have in mind would not work with the data as is, because it would need some recoding to match what I was trying to depict.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 400)
                .text("For instance, the deaths over time does not match the death locations data in a one-to-one, row by row, way.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 420)
                .text("So by utilizing IDs with data points and creating a new variable in my dataset to have the accumulative death counts, I was able to connect these two datasets.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 440)
                .text("It is an amazing feature being able to create interactivity in this way! D3 has the power!")
                .attr("fill", "black")
                .style("font-size", "14px")
                
               
    svg.append("text")
                .attr("x", 0)
                .attr("y", 472)
                .text("Rationale of Design")
                .attr("fill", "black")
                .style("font-size", "16px")
                .style("font-weight", "bold")
                
    svg.append("text")
                .attr("x", 0)
                .attr("y", 500)
                .text("First off, I wanted to have my map and time chart side by side so the user could interact with them and see what was happening on both visuals without having to zoom out. ")
                .attr("fill", "black")
                .style("font-size", "14px")
                

    svg.append("text")
                .attr("x", 0)
                .attr("y", 520)
                .text("I knew I wanted to keep the colors as simple as possible. ")
                .attr("fill", "black")
                .style("font-size", "14px")
                

    svg.append("text")
                .attr("x", 0)
                .attr("y", 540)
                .text("The colors I chose fall into the spectrum of the most common colors, special colors if you will, like yellow, orange, green, red, blue. ")
                .attr("fill", "black")
                .style("font-size", "14px")
                

    svg.append("text")
                .attr("x", 0)
                .attr("y", 560)
                .text("I wound up focusing on blues and greens aside from drawing and using text with black.")
                .attr("fill", "black")
                .style("font-size", "14px")
                

    svg.append("text")
                .attr("x", 0)
                .attr("y", 580)
                .text("I actually first chose red, green, and blues but after doing more research on colorblindness (three different types), it became clear to me that what seems easily distinguishable with reds and greens popping out against each other was actually not that easy to distinguish for people with Deuteranopia, for instance.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 600)
                .text("So with that in mind, I adjusted my color scheme, with the aid of Color Brewer, to be more friendly for folks who are colorblind.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 620)
                .text("This is how I wound up with the particular shades of blue and green on my map, those should still be distinguishable, on the level of luminance, for people looking at this with eyes that have different distributions of cones on their retinas than my own.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 640)
                .text("I also chose to have the affiliated death locations get highlighted in black as the user scolled over the bars on the time chart, and as the user scrolls out the death locations return to their original color.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 660)
                .text("I thought this would be helpful to distinguish all the deaths one had scrolled over up to that point, in other words, I thought this use of color could be a valuable investigative tool.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 690)
                .text("I also utilized some basic shapes to help distinguish the water pumps from the death locations.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 710)
                .text("I figured this were optimal for differentiating between these categories of variables.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 730)
                .text("I went ahead and manually made them rectangles with height being bigger than width. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 750)
                .text("I made this choice because it seemed to help these rectangles minimally overlap with existing data points, particularly in the center water pump near the brewery.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 780)
                .text("I added labels to the map for key locations like the brewery and workhouse.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 800)
                .text("I added a few major streets, but refrained from labelling many streets in an effort to keep this simple and legible.")
                .attr("fill", "black")
                .style("font-size", "14px")
               
    svg.append("text")
                .attr("x", 0)
                .attr("y", 820)
                .text(" This added tremendous value to the accuracy of the locations of the data points utilized. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 850)
                .text("I would have also liked to add the variables of age and sex to these visuals.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 870)
                .text("Unfortunately, I was hitting roadblocks but had I been able to do what I had in mind, it would have been adding different hues for the sex and utilizing luminance to distinguish the age groups. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 890)
                .text("There were six age groups altogether, so using luminance seems to be an optimal way to visualize this data and it also should be colorblind-friendly if used properly.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 910)
                .text("I would have other graphs showing the distributions of deaths in these groups (one for sex and one for age groups) and these would have interactive effects with the map similarly to how the deaths over time chart functions.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 950)
                .text("What I discovered:")
                .attr("fill", "black")
                .style("font-size", "16px")
                .style("font-weight", "bold")
                

    svg.append("text")
                .attr("x", 0)
                .attr("y", 980)
                .text("When I set out on this journey, I was curious to see how the death locations came together day to day. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1000)
                .text("With the way these visualizations are linked, I was able to see how that transpired. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1020)
                .text("My hypothesis was that we would see more deaths around the water pump that was the cause of the contamination. ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1040)
                .text("And each day, we would see the deaths piling up around that pump, so to speak.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1060)
                .text("What I saw, instead, was a spread of deaths all over the map that over time showed a clear clustering around that pump.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1080)
                .text("As I develop this further, with more variables, there will be much richer information to conceptualize and investigate with.")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1110)
                .text("Sources that helped me along the way on this journey in D3:")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1130)
                .text("Dr. Khari Reda and Ratanond Koonchanok – THANK YOU!!!")
                .attr("fill", "black")
                .style("font-size", "14px")
              
    svg.append("text")
                .attr("x", 0)
                .attr("y", 1150)
                .text("Websites:")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1170)
                .text("https://www.nationalgeographic.org/activity/mapping-london-epidemic/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1190)
                .text("https://alignedleft.com/tutorials/d3/drawing-svgs ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1210)
                .text("https://www.tutorialsteacher.com/d3js/data-binding-in-d3js ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1230)
                .text("https://jsfiddle.net/matehu/w7h81xz2/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1250)
                .text("http://embed.plnkr.co/wJDcZmkEzXaLVhuLZmcQ/")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1270)
                .text("https://bl.ocks.org/d3noob/8952219 ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1290)
                .text("https://blog.bitsrc.io/how-to-create-charts-from-external-data-sources-with-d3-js-4abbcb574706 ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1310)
                .text("https://blog.risingstack.com/d3-js-tutorial-bar-charts-with-javascript/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1330)
                .text("https://www.dashingd3js.com/svg-text-element ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1350)
                .text("https://stackoverflow.com/questions/20021833/using-svg-attributes-in-variables-in-d3")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1370)
                .text("https://www.tutorialsteacher.com/d3js/data-binding-in-d3js ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1390)
                .text("https://www.tutorialsteacher.com/codeeditor?cid=d3-22 ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1410)
                .text("https://www.d3indepth.com/introduction/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1430)
                .text("https://www.d3indepth.com/shapes/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1450)
                .text("https://www.d3indepth.com/scales/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1470)
                .text("https://www.d3indepth.com/selections/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1490)
                .text("https://resources.oreilly.com/examples/0636920026938/blob/master/chapter_09/27_data_join_with_key.html ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1510)
                .text("https://alignedleft.com/tutorials/d3/binding-data ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1530)
                .text("https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1550)
                .text("http://using-d3js.com/04_04_working_with_dates.html ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1570)
                .text("http://embed.plnkr.co/wJDcZmkEzXaLVhuLZmcQ/ ")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1590)
                .text("https://www.dashingd3js.com/lessons/d3-zoom-for-svg-lines-and-svg-paths-part-two")
                .attr("fill", "black")
                .style("font-size", "14px")

    svg.append("text")
                .attr("x", 0)
                .attr("y", 1610)
                .text("https://www.youtube.com/watch?v=wsCOif7RMBo&t=315s&ab_channel=AccioCode ")
                .attr("fill", "black")
                .style("font-size", "14px")


// YOUTUBE VIDEO LINK
         /*       <iframe width="560" height="315" src="https://www.youtube.com/embed/KSbvF2ZIcYw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe> */
               
         svg.append("text")
                .attr("x", 0)
                .attr("y", -105)
                .text("Watch my walkthrough video here:")
                .attr("fill", "black")
                .style("font-size", "18px")
                .style("font-weight", "bold")

        svg.append("text")
                .attr("x", 0)
                .attr("y", -85)
                .text("https://www.youtube.com/watch?v=c1LbA3HCAsM&feature=youtu.be")
                .attr("fill", "darkgreen")
                .style("font-size", "16px")
                
                
        svg.append("text")
                .attr("x", 0)
                .attr("y", -65)
                .text("(in-screen video located below project description)")
                .attr("fill", "black")
                .style("font-size", "18px")
                .style("font-weight", 'italics');
               
        
	</script>
</body>
<iframe width="560" height="315" src="https://www.youtube.com/embed/c1LbA3HCAsM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

</html>
