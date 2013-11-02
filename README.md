D3.js Force Directed Layout Link Filter
=======================================

Add link filter functionality to force-directed-layout implemented in [D3.js](http://d3js.org/). Use the basic code from [Mobile Patent Suits](http://bl.ocks.org/mbostock/1153292) example implemented in D3.js.

**[Try Yourself!](http://jsfiddle.net/zhanghuancs/sdRR2/)**

Overview
--------
- Create a checkbox filter for each link type
- Link type checkbox filter group is created on fly
- By default, all link types are checked, which means the graph displays all links at the first time
- Uncheck the link type will hide the links of that type
- If a node's all connected links are invisible, the node itself will be invisible

Create Link Type Filter
-----------------------
To create a link type filter, we firstly create a filter container. The auto generated checkbox `<div>` will be appended into `<div>` with `id="filterContainer"`. We set `style="display: none;"` to hide the container. We will show it after the layout is rendered.

```html
<div id="sidebar" style="display: none;">
    <div class="item-group">
        <label class="item-label">Filter</label>  
        <div id="filterContainer" class="filterContainer checkbox-interaction-group"></div>
    </div>
</div>	
```

To generate checkbox filter, using d3.js to create checkbox `<div>` for each link type and register `click` event. 

```javascript
// method to create filter
function createFilter()
{
    d3.select(".filterContainer").selectAll("div")
      .data(["licensing", "suit", "resolved"])
      .enter()
      .append("div")
          .attr("class", "checkbox-container")
      .append("label")
      .each(function(d) {
         // create checkbox for each data
	  d3.select(this).append("input")
	    .attr("type", "checkbox")
	    .attr("id", function(d) {return "chk_" + d;})
	    .attr("checked", true)
	    .on("click", function(d, i) {
			// register on click event
			var lVisibility = this.checked? "visible":"hidden";
			filterGraph(d, lVisibility);
	    })
	    d3.select(this).append("span")
	      .text(function(d){return d;});
      });
							
      $("#sidebar").show();  // show sidebar
}
```

Filter Graph
------------
First check each path, hide/show each path based on if the path type is unchecked/checked in the checkbox filter group. 

Then check each node, if node's all connected path is invisible, the node itself should be invisible.

```javascript
// Method to filter graph
function filterGraph(aType, aVisibility)
{	
	// change the visibility of the connection path
	path.style("visibility", function(o) {
		var lOriginalVisibility = $(this).css("visibility");
		return o.type === aType ? aVisibility : lOriginalVisibility;
	});	
					        
	// change the visibility of the node
	// if all the links with that node are invisibile, the node should also be invisible
	// otherwise if any link related to that node is visibile, the node should be visible
	circle.style("visibility", function(o, i) {	
		var lHideNode = true;
		path.each(function(d, i){
			if(d.source === o || d.target === o)
			{
				if($(this).css("visibility") === "visible")
				{
					lHideNode = false;
					// we need show the text for this circle
					d3.select(d3.selectAll(".nodeText")[0][i]).style("visibility","visible");
					return "visible";
				}
			}
		});
		if(lHideNode)
		{
			// we need hide the text for this circle 
			d3.select(d3.selectAll(".nodeText")[0][i]).style("visibility","hidden");
			return "hidden";
		}
	});
}
```
