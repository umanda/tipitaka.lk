<template>
  <div>
    <span class="root py-2" @click="openSutta" :style="$store.getters['styles']">
      <span class="pitaka-icon mr-2 pa-1">{{ items[0].text }}</span>
      
      <span class="sutta-name mr-2">
        {{ suttaNameItem.text.replace(/[\(\)\[\]]/g, '') }}
      </span>

      <span v-for="(item, i) in items.slice(1, -1)" :key="item.key" class="parents">
        <v-icon v-if="i > 0">mdi-chevron-right</v-icon>
        <span>{{ item.text }}</span>
      </span>    
    </span>
    
    <BookmarkIcon :entry="params" class="ml-1" />
  </div>
</template>

<style scoped>
.root { cursor: pointer; }
.root:hover { color: var(--v-info-base); }
.pitaka-icon { font-size: 1.1em; border: 1px solid; border-radius: 10px; }
.sutta-name { font-size: 1.2em; color: var(--v-primary-base); }
/** .root:hover .sutta-name { text-decoration: underline; }*/
.parents { text-decoration: none; font-size: 1.1em; }
</style>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'TipitakaLink',
  props: {
    itemKey: String,
    params: Object,
  },
  data() {
    return {
      keyToText: { 'vp': 'VP', 'sp': 'SP', 'ap': 'AP' },
    }
  },
  computed: {
    ...mapGetters('tree', ['getKey', 'getName']),
    items() {
      const items = [this.getKey(this.itemKey)]
      let parent = items[0].parent
      
      while (parent != 'root') {
        items.push(this.getKey(parent))
        parent = items[items.length - 1].parent
      }
      return items.reverse().map(item => ({
        text: this.getLinkText(item),
        key: item.key, // router link
        disabled: !item.filename, // empty filename means disabled
      }))
    },
    suttaNameItem() { return this.items.slice(-1)[0] },
  },
  methods: {
    openSutta() {
      const params = { key: this.itemKey, ...this.params }
      this.$store.dispatch('tabs/openAndSetActive', params)
    },
    getLinkText(item) {
      return this.keyToText[item.key] || this.getName(item.key, this.params.language)
    }
  },

}
</script>