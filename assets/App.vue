<template lang="pug">
  v-fade-transition(appear)
    v-app(v-cloak v-if="!examples")
      app-drawer
      app-toolbar
      app-view
      app-footer
      app-fab

    div(v-else)#app
      router-view
</template>

<script>
  import AppDrawer from '@/components/core/AppDrawer'
  import AppFab from '@/components/core/AppFab'
  import AppFooter from '@/components/core/AppFooter'
  import AppToolbar from '@/components/core/AppToolbar'
  import AppView from '@/components/core/AppView'
  import Meta from '@/mixins/meta'

  export default {
    name: 'documentation',

    components: {
      AppDrawer,
      AppFab,
      AppFooter,
      AppToolbar,
      AppView
    },

    mixins: [Meta],

    computed: {
      examples () {
        return !!this.$route.params.example
      }
    },

    watch: {
      $route (current, previous) {
        this.setupLayout(200)
      }
    },

    created () {
      this.setupLayout()
      this.getReleases()
    },

    methods: {
      getReleases () {
        this.$http.get('/releases/releases.json').then(({ data }) => {
          this.$store.commit('app/RELEASES', data)
        }).catch(err => {
          console.log(err)
        })
      },
      setupLayout (timeout = 300) {
        const drawer = this.$route.fullPath !== '/'
        this.$store.commit('app/STATELESS', !drawer)

        setTimeout(() => {
          if (this.$route &&
            this.$route.fullPath !== '/' &&
            this.$route.from &&
            this.$route.from.fullPath !== '/'
          ) return

          if (this.$vuetify.breakpoint.mdAndDown) return

          this.$store.commit('app/DRAWER', drawer)
        }, timeout)
      }
    }
  }
</script>

<style lang="stylus">
  @import '../node_modules/vuetify/src/stylus/settings/_elevations.styl'

  [v-cloak]
    display: none
</style>
