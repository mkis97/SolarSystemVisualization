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
                    obj.value = item.gravity
                    data.push(obj)
                })


                var margin = {top: 10, right: 10, bottom: 10, left: 10},
                    width = 460 - margin.left - margin.right,
                    height = 460 - margin.top - margin.bottom,
                    innerRadius = 80,
                    outerRadius = Math.min(width, height) / 2;

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

                svg.append("g")
                    .selectAll("path")
                    .data(data)
                    .enter()
                    .append("path")
                    .attr("fill", "#69b3a2")
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
            }
        }
    }
</script>
