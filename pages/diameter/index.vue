<template>
  <v-container fluid fill-height id="initDi">
    <v-row justify="center">
      <v-card width="500" height="500" class="body" style="border-radius: 16px; position: relative" id="init">
      </v-card>
    </v-row>
    <v-row justify="end">
      <v-card class="legend mr-5" style="border-radius: 16px">
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
                    obj.value = item.diameter
                    data.push(obj)
                })

                var width = 500;
                var height = 500;
                var outerRadius = 220;
                var innerRadius = 0;

                const color = d3.scaleOrdinal(d3.schemeCategory10);

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", width)
                    .attr("height", height);

                const pie = d3.pie()
                    .value(function (d) {
                        return d.value;
                    });

                const arc = d3.arc()
                    .innerRadius(innerRadius)
                    .outerRadius(outerRadius);

                var pieArcs = svg.selectAll("g.pie")
                    .data(pie(data))
                    .enter()
                    .append("g")
                    .attr("class", "pie")
                    .attr("transform", "translate(" + (width / 2) + ", " + (height / 2) + ")");

                const div = d3
                    .select('.body')
                    .append('div')
                    .attr('class', 'tooltip')
                    .style('opacity', 0);

                pieArcs.append("path")
                    .attr("fill", function (d, i) {
                        return color(i);
                    })
                    .attr("d", arc)
                    .on("mouseover", function (d) {
                        setTimeout(function () {
                            var x = document.getElementsByClassName("tooltip")
                            x[0].className = 'hidden'
                        }, 3000)
                        div.transition()
                            .duration(200)
                            .attr("class", "tooltip")
                            .style("opacity", 1);
                        div.html(d.data.name + ' - ' + d.data.value + ' km')
                            .style("left", (d3.event.pageX - 500) + "px")
                            .style("top", (d3.event.pageY - 80) + "px")
                    })

                var lgnd = d3.select(".legend")
                    .append("svg")
                    .attr("width", 200)
                    .attr("height", 185)

                var labels = ["Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto"]

                lgnd.selectAll("ld")
                    .data(labels)
                    .enter()
                    .append("circle")
                    .attr("cx", 60)
                    .attr("cy", function (d, i) {
                        return 15 + i * 20
                    })
                    .attr("r", 7)
                    .style("fill", function (d, i) {
                        return color(i)
                    })

                lgnd.selectAll("ll")
                    .data(labels)
                    .enter()
                    .append("text")
                    .attr("x", 90)
                    .attr("y", function (d, i) {
                        return 15 + i * 20
                    })
                    .style("fill", function (d, i) {
                        return color(i)
                    })
                    .text(function (d) {
                        return d
                    })
                    .style("alignment-baseline", "middle")
            },
        }
    }
</script>


<style>
  #initDi {
    height: 100%;
    width: 100%;
    background: url(../../static/space.jpg);
    background-size: cover;
  }

  .hidden {
    display: none;
  }

  .tooltip {
    width: 175px;
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

