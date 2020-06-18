<template>
  <v-app>
    <v-navigation-drawer permanent app fixed>
      <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          link
          @click="$router.push(`${item.to}`)"
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-content>
      <nuxt/>
    </v-content>
  </v-app>
</template>

<script>
    export default {
        data() {
            return {
                items: [
                    {
                        icon: 'mdi-rotate-3d-variant',
                        title: 'Rotation',
                        to: '/'
                    },
                    {
                        icon: 'mdi-diameter-variant',
                        title: 'Diameter',
                        to: '/diameter'
                    },
                    {
                        icon: 'mdi-moon-full',
                        title: 'Density',
                        to: '/density'
                    },
                    {
                        icon: 'mdi-map-marker-distance',
                        title: 'Distance from Sun',
                        to: '/distance_from_sun'
                    },
                    {
                        icon: 'mdi-download',
                        title: 'Gravity',
                        to: '/gravity'
                    },
                    {
                        icon: 'mdi-scatter-plot-outline',
                        title: 'Satellites',
                        to: '/satellites'
                    }
                ],
            }
        }
    }
</script>
