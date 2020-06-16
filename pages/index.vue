<template>
  <v-container fluid id="init">
    <v-row justify="center">
      <v-card width="700" height="700" class="body" id="init">
      </v-card>
    </v-row>
  </v-container>
</template>

<script>
    import d3 from "../plugins/d3"

    export default {
        mounted() {
            this.renderRotation()
        },

        methods: {
            renderRotation() {
                var planets = [
                    {"ind": 0, "radius": 0, "speed": 1, "start": undefined},
                    {"ind": 1, "radius": 1, "speed": 2000, "start": undefined},
                    {"ind": 2, "radius": 2, "speed": 6000, "start": undefined},
                    {"ind": 3, "radius": 3, "speed": 10000, "start": undefined},
                    {"ind": 4, "radius": 4, "speed": 19000, "start": undefined},
                    {"ind": 5, "radius": 5, "speed": 119000, "start": undefined},
                    {"ind": 6, "radius": 6, "speed": 295000, "start": undefined},
                    {"ind": 7, "radius": 7, "speed": 840000, "start": undefined},
                    {"ind": 8, "radius": 8, "speed": 1648000, "start": undefined},
                ];
                var width = 700;
                var height = 700;
                if (width > height) {
                    var max = height
                } else {
                    var max = width
                }
                var orbit = d3.scaleLinear()
                    .domain([0, 10])
                    .range([0, max / 2]);
                var svg = d3.select(".body")
                    .append("svg")
                    .attr("width", width)
                    .attr("height", height);
                var all = svg.selectAll("circle")
                    .data(planets)
                    .enter()
                all.append("circle")
                    .classed("planet", true)
                    .attr("id", function (d) {
                        return "planet" + d.ind
                    })
                    .attr("cx", function (d) {
                        return width / 2 + orbit(d.radius);
                    })
                    .attr("cy", height / 2)
                    .attr("r", 10)
                    .style("fill", "#a0a0a0");
                d3.timer(tickFn);

                function tickFn(_elapsed) {
                    var timer_elapsed = _elapsed;
                    for (var i = 0; i < planets.length; i++) {
                        var t_circleData = planets[i];
                        if (t_circleData.start == undefined) {
                            t_circleData.start = _elapsed;
                        }
                        ;
                        var t_elapsed = _elapsed - t_circleData.start;
                        var t = t_elapsed / t_circleData.speed;
                        var rotation_radius = t_circleData.radius;
                        var t_angle = (2 * Math.PI) * t;
                        var t_x = rotation_radius * Math.cos(t_angle);
                        var t_y = rotation_radius * Math.sin(t_angle);
                        t_circleData.x = t_x - rotation_radius;
                        t_circleData.y = t_y;
                    }
                    var t_circle = svg.selectAll(".planet");
                    t_circle
                        .attr("transform", function (d) {
                            return "translate(" + orbit(d.x) + "," + orbit(d.y) + ")"
                        });
                }
            }
        }
    }
</script>


<style>
  #init {
    height: 100%;
    width: 100%;
    background: url(../static/space.jpg);
    background-size: cover;
  }
</style>
