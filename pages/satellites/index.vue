<template>
  <v-container fluid fill-height>
    <v-row justify="center">
      <v-card width="500" height="500" class="body">
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
    import d3 from "../../plugins/d3"

    export default {
        data() {
            return {
                plan: []
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
                    this.plan = res.data
                    console.log(this.plan)
                } catch (err) {
                    console.log(err)
                }
            },

            renderGraph() {
                var data = [];

                this.plan.forEach((item, index) => {
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
                    .force("center", d3.forceCenter().x(width / 2).y(height / 2)) // Attraction to the center of the svg area
                    .force("charge", d3.forceManyBody().strength(1)) // Nodes are attracted one each other of value is > 0
                    .force("collide", d3.forceCollide().strength(.1).radius(15).iterations(1)) // Force that avoids circle overlapping

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
            }
        }
    }
</script>
