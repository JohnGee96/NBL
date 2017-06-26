D3 JS Data Visualization Library

** Reference **

  Styling Properties (SVG): https://www.w3.org/TR/SVG/styling.html
  D3 Library Reference: https://github.com/d3/d3-zoom/blob/master/README.md

** Tutorial and Example **

  Build a Histogram: https://bost.ocks.org/mike/bar/1/
  Scrollable Graphs: 
    1. http://blog.scottlogic.com/2014/09/19/interactive.html
    2. https://codepen.io/ghiden/pen/EhBpy


** SVG Specifics **

  * SVG elements must be positioned relative to the top-left corner of the container; SVG does 
    not support flow layout or even text wrapping
  * SVG requires text to be placed explicitly in text elements
  * text elements do not support padding or margins, the text position must be offset by three pixels from the end of the bar, while the dy offset is used to center the text vertically.
  

** Data Joining **

  There are 3 states for d3 data joins:
  1. Enter
  2. Update
  3. Exit

  svg.selectAll("circle")                  ## select "circle" from parent node
  .data(data)                              ##
  .enter().append("circle")
    .attr("cx", function(d) { return d.x; })
    .attr("cy", function(d) { return d.y; })
    .attr("r", 2.5);


** Scaling **

  Scales data to fit in specified domain and range:

  var x = d3.scale.linear()
    .domain([0, d3.max(data)])
    .range([0, 420]);

  although x here looks like an object, it is also a function that returns the scaled display value in the range for a given data value in the domain. For example, an input value of 4 returns 40, and an input value of 16 returns 160. To use the new scale, simply replace the hard-coded multiplication by calling the scale function:

  d3.select(".chart")
    .selectAll("div")
      .data(data)
    .enter().append("div")
      .style("width", function(d) { return x(d) + "px"; })
      .text(function(d) { return d; });