<template>
  <v-container fluid fill-height id="initD">
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
                    obj.value = item.distanceFromSun
                    data.push(obj)
                })

                var margin = {top: 20, right: 20, bottom: 60, left: 50},
                    width = 500 - margin.left - margin.right,
                    height = 500 - margin.top - margin.bottom;

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

                const div = d3
                    .select('.body')
                    .append('div')
                    .attr('class', 'tooltip')
                    .style('opacity', 0);

                svg.append("g")
                    .attr("transform", "translate(0," + height + ")")
                    .call(d3.axisBottom(x))
                    .selectAll("text")
                    .attr("transform", "translate(-10,10)rotate(-60)")
                    .style("text-anchor", "end");

                var y = d3.scaleLinear()
                    .domain([0, 7000])
                    .range([height, 0]);

                svg.append("g")
                    .call(d3.axisLeft(y));

                svg.selectAll("b")
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
                    .attr("fill", "#9400D3")
                    .on("mouseover", function (d) {
                        setTimeout(function () {
                            var x = document.getElementsByClassName("tooltip")
                            x[0].className = 'hidden'
                        }, 1500)
                        div.transition()
                            .duration(200)
                            .attr("class", "tooltip")
                            .style("opacity", 1);
                        div.html(d.name + ' - ' + d.value + ' million km')
                            .style("left", (d3.event.pageX - 500) + "px")
                            .style("top", (d3.event.pageY - 150) + "px")
                    })
            }
        }
    }
</script>


<style>
  #initD {
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
