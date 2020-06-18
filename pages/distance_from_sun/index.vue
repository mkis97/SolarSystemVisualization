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

                this.plan.forEach(item => {
                    console.log(item)
                    let obj = {name: "", value: null}
                    obj.name = item.name
                    obj.value = item.distanceFromSun
                    data.push(obj)
                })


                var margin = {top: 30, right: 30, bottom: 70, left: 60},
                    width = 460 - margin.left - margin.right,
                    height = 400 - margin.top - margin.bottom;

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", width + margin.left + margin.right)
                    .attr("height", height + margin.top + margin.bottom)
                    .append("g")
                    .attr("transform",
                        "translate(" + margin.left + "," + margin.top + ")");

                var x = d3.scaleBand()
                    .range([0, width])
                    .domain(data.map(function (d) {
                        return d.name;
                    }))
                    .padding(0.2);
                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x))
                    .selectAll("text")
                    .attr("transform", "translate(-10,0)rotate(-45)")
                    .style("text-anchor", "end");

                var y = d3.scaleLinear()
                    .domain([0, 7000])
                    .range([height, 0]);
                svg.append("g")
                    .call(d3.axisLeft(y));

                svg.selectAll("mybar")
                    .data(data)
                    .enter()
                    .append("rect")
                    .attr("x", function (d) {
                        return x(d.name);
                    })
                    .attr("y", function (d) {
                        return y(d.value);
                    })
                    .attr("width", x.bandwidth())
                    .attr("height", function (d) {
                        return height - y(d.value);
                    })
                    .attr("fill", "#69b3a2")
            }
        }
    }
</script>
