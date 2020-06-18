<template>
  <v-container fluid fill-height  id="init">
    <v-row justify="center">
      <v-card width="500" height="500" class="body" style="border-radius: 16px">
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
                } catch (err) {
                    console.log(err)
                }
            },

            renderGraph() {
                var data = [];

                this.plan.forEach(item => {
                    let obj = {name: "", value: null}
                    obj.name = item.name
                    obj.value = item.density
                    data.push(obj)
                })

                var margin = {top: 40, right: 10, bottom: 90, left: 50},
                    width = 500 - margin.left - margin.right,
                    height = 500 - margin.top - margin.bottom;

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("height", height + margin.top + margin.bottom)
                    .attr("width", width + margin.left + margin.right)
                    .append("g")
                    .attr("transform",
                        "translate(" + margin.left + "," + margin.top + ")");

                var x = d3.scaleBand()
                    .range([ 0, width ])
                    .domain(data.map(function (d) {
                        return d.name;
                    }))
                    .padding(1);

                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x))
                    .selectAll("text")
                    .attr("transform", "translate(-10,10)rotate(-60)")
                    .style("text-anchor", "end");

                var y = d3.scaleLinear()
                    .domain([0, 6000])
                    .range([ height, 0]);

                svg.append("g")
                    .call(d3.axisLeft(y));

                svg.selectAll("myline")
                    .data(data)
                    .enter()
                    .append("line")
                    .attr("x1", function(d) { return x(d.name); })
                    .attr("x2", function(d) { return x(d.name); })
                    .attr("y1", function(d) { return y(d.value); })
                    .attr("y2", y(0))
                    .attr("stroke", "grey")

                svg.selectAll("mycircle")
                    .data(data)
                    .enter()
                    .append("circle")
                    .attr("cx", function(d) { return x(d.name); })
                    .attr("cy", function(d) { return y(d.value); })
                    .attr("r", "7")
                    .style("fill", "#69b3a2")
                    .attr("stroke", "black")
            }
        }
    }
</script>


<style>
  #init {
    height: 100%;
    width: 100%;
    background: url(../../static/space.jpg);
    background-size: cover;
  }
</style>
