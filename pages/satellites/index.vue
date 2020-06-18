<template>
  <v-container fluid fill-height id="initS">
    <v-row justify="center">
      <v-card width="500" height="500" class="body" style="border-radius: 16px">
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

                this.planets.forEach((item, index) => {
                    let moons = item.numberOfMoons
                    for (let i = 0; i < moons; i++) {
                        let obj = {name: "", group: null}
                        obj.name = item.name
                        obj.group = index
                        data.push(obj)
                    }
                })

                var width = 500
                var height = 500

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", 500)
                    .attr("height", 500)

                var x = d3.scaleOrdinal()
                    .domain([2, 3, 4, 5, 6, 7, 8])
                    .range([10, 20, 30, 40, 50, 60, 70])

                var color = d3.scaleOrdinal()
                    .domain([2, 3, 4, 5, 6, 7, 8])
                    .range(["#9400D3", "#FF0000", "#00FF00", "#FF7F00", "#4B0082", "#0000FF", "#FFFF00"])

                var node = svg.append("g")
                    .selectAll("circle")
                    .data(data)
                    .enter()
                    .append("circle")
                    .attr("r", 7)
                    .attr("cx", width / 2)
                    .attr("cy", height / 2)
                    .style("fill", function (d) {
                        return color(d.group)
                    })
                    .style("fill-opacity", 0.8)
                    .attr("stroke", "black")
                    .style("stroke-width", 4)

                var simulation = d3.forceSimulation()
                    .force("x", d3.forceX().strength(0.5).x(function (d) {
                        return x(d.group)
                    }))
                    .force("y", d3.forceY().strength(0.1).y(height / 2))
                    .force("center", d3.forceCenter().x(width / 2).y(height / 2))
                    .force("charge", d3.forceManyBody().strength(1))
                    .force("collide", d3.forceCollide().strength(.1).radius(15).iterations(1))

                simulation
                    .nodes(data)
                    .on("tick", function (d) {
                        node
                            .attr("cx", function (d) {
                                return d.x;
                            })
                            .attr("cy", function (d) {
                                return d.y;
                            })
                    });


                var lgnd = d3.select(".legend")
                    .append("svg")
                    .attr("width", 200)
                    .attr("height", 165)

                var labels = ["Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto"]

                var colors = ["#9400D3", "#FF0000", "#00FF00", "#FF7F00", "#4B0082", "#0000FF", "#FFFF00"]

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
                        return colors[i]
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
                        return colors[i]
                    })
                    .text(function (d) {
                        return d
                    })
                    .style("alignment-baseline", "middle")
            }
        }
    }
</script>

<style>
  #initS {
    height: 100%;
    width: 100%;
    background: url(../../static/space.jpg);
    background-size: cover;
  }
</style>
