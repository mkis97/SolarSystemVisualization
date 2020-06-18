<template>
  <v-container fluid fill-height id="initG">
    <v-row justify="center">
      <v-card width="500" height="500" class="body" id="initG">
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
    import d3 from "../../plugins/d3"

    export default {
        data() {
            return {
                planets: []
            }
        },

        async mounted() {
            await this.getData()
            this.renderGraph()
        },

        methods: {
            async getData() {
                try {
                    let res = await this.$axios.get('https://raw.githubusercontent.com/devstronomy/nasa-data-scraper/master/data/json/planets.json')
                    this.planets = res.data
                } catch (err) {
                    console.log(err)
                }
            },

            renderGraph() {
                var data = [];

                this.planets.forEach(item => {
                    let obj = {name: "", value: null}
                    obj.name = item.name
                    obj.value = item.gravity
                    data.push(obj)
                })

                var margin = {top: 10, right: 10, bottom: 10, left: 10},
                    width = 500 - margin.left - margin.right,
                    height = 500 - margin.top - margin.bottom,
                    innerRadius = 100,
                    outerRadius = 500;

                const color = d3.scaleOrdinal(d3.schemeCategory10);

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", width + margin.left + margin.right)
                    .attr("height", height + margin.top + margin.bottom)
                    .append("g")
                    .attr("transform", "translate(" + width / 2 + "," + (height / 2) + ")");

                var x = d3.scaleBand()
                    .range([0, 2 * Math.PI])
                    .align(0)
                    .domain(data.map(function (d) {
                        return d.name;
                    }));

                var y = d3.scaleLinear()
                    .range([innerRadius, outerRadius])
                    .domain([0, 60]);

                const div = d3
                    .select('.body')
                    .append('div')
                    .attr('class', 'tooltip')
                    .style('opacity', 0);

                svg.append("g")
                    .selectAll("path")
                    .data(data)
                    .enter()
                    .append("path")
                    .attr("fill", function (d, i) {
                        return color(i);
                    })
                    .attr("d", d3.arc()
                        .innerRadius(innerRadius)
                        .outerRadius(function (d) {
                            return y(d.value);
                        })
                        .startAngle(function (d) {
                            return x(d.name);
                        })
                        .endAngle(function (d) {
                            return x(d.name) + x.bandwidth();
                        })
                        .padAngle(0.01)
                        .padRadius(innerRadius))
                    .on("mouseover", function (d) {
                        setTimeout(function () {
                            var x = document.getElementsByClassName("tooltip")
                            x[0].className = 'hidden'
                        }, 2500)
                        div.transition()
                            .duration(200)
                            .attr("class", "tooltip")
                            .style("opacity", 1);
                        div.html(d.name + ' - ' + d.value + ' m/s2')
                            .style("left", (d3.event.pageX - 500) + "px")
                            .style("top", (d3.event.pageY - 150) + "px")
                    })
            }
        }
    }
</script>

<style>
  #initG {
    height: 100%;
    width: 100%;
    background: url(../../static/space.jpg);
    background-size: cover;
  }

  .hidden {
    display: none;
  }

  .tooltip {
    width: 250px;
    height: 50px;
    background: #47494e;
    position: absolute;
    padding: 10px;
    text-align: center;
    font-weight: 700;
    font-size: 16px;
    border-radius: 16px;
  }
</style>
