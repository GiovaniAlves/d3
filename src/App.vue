<template>
    <div id="app">
        <div id="linechart" />
        <br>
        <button @click="addLine">Add Lines</button>
    </div>
</template>

<script>
import * as d3 from 'd3';

export default {
    name: 'App',
    data() {
        return {
            svg: undefined,
            chart_data: [
                {x: 5, y: 25},
                {x: 20, y: 52},
                {x: 30, y: 78},
                {x: 40, y: 45},
                {x: 50, y: 45},
                {x: 60, y: 78},
                {x: 70, y: 785},
                {x: 80, y: 54},
                {x: 90, y: 45},
                {x: 100, y: 75},
                {x: 150, y: 250},
                {x: 180, y: 210},
                {x: 240, y: 30},
                {x: 250, y: 0},
            ],
            xScale: null,
            yScale: null,
            height: 700,
            width: 1000,
            positionLine: 20
        }
    },
    mounted() {
        let margin = { top: 20, right: 50, bottom: 30, left: 40 };
        let width = this.width - margin.left - margin.right;
        let height = this.height - margin.top - margin.bottom;

        //Add SVG
        this.svg = d3.select("div#linechart")
            .append("svg")
            .attr("width", this.width + margin.left + margin.right)
            .attr("height", this.height + margin.top + margin.bottom)
            .attr("xmlns", 'http://www.w3.org/2000/svg')
            .append("g")
            .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
        d3.sc

        // Add X axis
        this.xScale = d3.scaleLinear()
            .domain(d3.extent(this.chart_data, function (d) { return d.x; }))
            .range([0, width]);
        this.svg.append("g")
            .attr("transform", "translate(0," + height + ")")
            .call(d3.axisBottom(this.xScale))

        // Add Y axis
        this.yScale = d3.scaleLinear()
            .domain([0, d3.max(this.chart_data, function (data) { return +data.y; })])
            .range([height, 0]);
        this.svg.append("g")
            .attr("transform", "translate(0, 0)")
            .call(d3.axisLeft(this.yScale));

        // Plot of data in the form of line
        let xScale = this.xScale;
        let yScale = this.yScale;
        d3.select('svg').append("path")
            .datum(this.chart_data)
            .attr("fill", "none")
            .attr("stroke", "steelblue")
            .attr("stroke-width", 1.5)
            .attr("d", d3.line()
                .x(function (data) { return xScale(data.x) })
                .y(function (data) { return yScale(data.y) })
            )
            .attr("transform", "translate(40, 0)");

        var squareData = [
            {x:105, y:170, width:15, height:500},
            {x:205, y:170, width:15, height:500},
            {x:305, y:170, width:15, height:500},
            {x:405, y:170, width:15, height:500},
            {x:555, y:170, width:15, height:500},
            {x:700, y:170, width:15, height:500},
        ];
        let lines = d3.select('svg').selectAll("rect")
            .data(squareData)
            .enter()
            .append("rect")
            .attr("class", (data,i)=>`rectGroup line_${i+1}`)
            .attr("x", data => data.x)
            .attr("y", data => data.y)
            .attr("height", data => data.height)
            .attr("width", data => data.width)
            .attr("fill", "black")

        let drag = d3.drag()
        .on("drag", function(event) {
            var deltaX = event.x - d3.select(this).attr("x");
            d3.select(this).attr("x", event.x); // move one line

            var line = d3.select(this).attr("class").split(/\s+/).filter(s=>s.startsWith("line_"))[0];
            console.log(line, deltaX)

            d3.selectAll(".rectGroup").attr("x", d=>d.x = d.x+deltaX)
        });

        lines.call(drag);
    },
    methods: {
        addLine () {
            this.svg.append("rect")
                .attr("x", this.xScale(this.positionLine))
                .attr("y", 150)
                .attr("height", 500)
                .attr("width", 15)
                .style("fill", "#000")
                .attr("cursor", "move")

            this.positionLine +=30
        }
    }
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
.draggable {
    cursor: move;
}
</style>
