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
                var width = 500;
                var height = 500;
                var outerRadius = 200;
                var innerRadius = 0;

                const color = d3.scaleOrdinal(d3.schemeCategory10);

                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", width)
                    .attr("height", height);


                const pie = d3.pie()
                    .value(function (d) { return d.value; });

                const arc = d3.arc()
                    .innerRadius(innerRadius)
                    .outerRadius(outerRadius);


                var data = [];

                this.plan.forEach(item=>{
                    console.log(item)
                    let obj= {name: "", value: null}
                    obj.name=item.name
                    obj.value=item.diameter
                    data.push(obj)
                })

                console.log(data)

                var pieArcs = svg.selectAll("g.pie")
                    .data(pie(data))
                    .enter()
                    .append("g")
                    .attr("class", "pie")
                    .attr("transform", "translate(" + (width / 2) + ", " + (height / 2) + ")");

                pieArcs.append("path")
                    .attr("fill", function (d, i) { return color(i); })
                    .attr("d", arc);

                pieArcs.append("text")
                    .attr("transform", function (d) { return "translate(" + arc.centroid(d) + ")"; }).attr("text-anchor", "middle")
                    .text(function (d) { return `${d.data.name} - ${d.value}` });
            }
        }
    }
</script>
