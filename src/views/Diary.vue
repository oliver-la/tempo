<template>
  <div class="pb-8">
    <v-container>
      <v-tabs
        show-arrows
        centered
        height="36"
        background-color="transparent">
        <v-tab :to="{path: '/diary', query: {q: query}}" exact :key="entries.length">
          Entries · {{ entries.length }}
        </v-tab>
        <v-tab :to="{path: '/diary/calendar', query: {q: query}}">Calendar</v-tab>
        <v-tab :to="{path: '/diary/visualization', query: {q: query}}">Visualization</v-tab>
        <v-tab :to="{path: '/diary/advanced', query: {q: query}}">Advanced</v-tab>
      </v-tabs>
    </v-container>
    <router-view
      :all-entries="entries"
      @deleted="handleDelete"
      @updated="handleUpdate"
    ></router-view>
  </div>
</template>

<script>
import {search} from '@/utils'

export default {
  props: {
    query: String
  },
  data () {
    return {
      entries: [],
    }
  },
  async created () {
    this.search()
  },
  methods: {    
    
    async handleDelete (entry) {
      this.entries = this.entries.filter((e) => {
        return e._id != entry._id
      })
    },
    async handleUpdate (entry) {
      this.entries.forEach((e) => {
        if (e._id === entry._id) {
          Object.assign(e, entry)
        }
      })
    },
    async search () {
      this.entries = await search({
        store: this.$store,
        sortDesc: this.sortDesc,
        query: this.query
      })
    },
  },
  watch: {
    async query () {
      await this.search()
    },
    "$store.state.lastSync": {
      async handler () {
        await this.search()
      }
    },
  },
}
</script>
