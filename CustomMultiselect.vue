<template>
  <div v-on-click-outside="deactivate">
    <div class="selectbar" @click.prevent="activate()">
      <input
        v-if="showInput"
        :value="searchedText"
        @input="searchedText = $event.target.value"
        @focus.prevent="activate()"
        @keyup.esc="deactivate()"
        class="selectbar__input"
        type="text"
        placeholder="Type to search"
        ref="search"
        id="search"
        autocomplete="off"
      >

      <div v-else class="selectbar__span">
        {{ selectedItemName }}
      </div>

    </div>

    <div v-if="showList" class="allitems">
      <ul>
        <li class="allitems__list" v-for="(item) in filteredAllItems" @click="atSelectItem(item)" :key="item.id">
          <span>{{ item.name }}</span>
        </li>
      </ul>
    </div>

  </div>
</template>

<script>
import { mixin as onClickOutside } from 'vue-on-click-outside'
import { EVENT_CATEGORY_SELECTION_CUSTOM_MULTISELECT } from '~/store/constants.yaml'

import filter from 'lodash/filter'
import sortBy from 'lodash/sortBy'

export default {
  props: ['items', 'selectedItem'],
  mixins: [onClickOutside],
  data () {
    return {
      searchedText: null,
      showList: false
    }
  },
  computed: {
    filteredAllItems () {
      if (this.searchedText) {
        let filteredItems = filter(this.items, (i) => {
          return i.name.toLowerCase().includes(this.searchedText.toLowerCase())
        })
        return sortBy(filteredItems, ['id'])
      } else {
        return sortBy(this.items, ['id'])
      }
    },
    selectedItemName () {
      return this.selectedItem && this.selectedItem.name || ''
    },
    showInput () {
      return !this.selectedItem || this.showList
    }
  },
  methods: {
    atSelectItem (item) {
      this.$root.$emit(EVENT_CATEGORY_SELECTION_CUSTOM_MULTISELECT, item)
      this.selectedItem = item
      this.deactivate()
    },
    activate () {
      this.showList = true
      console.log(document.getElementById('search'))
    },
    deactivate () {
      this.searchedText = null
      this.showList = false
    }
  }
}
</script>

<style lang="sass" scoped>
  .selectbar
    &__input
      background-color: #ffffff
      border: 1px solid #dbdbdb
      border-radius: 4px
      color: #363636
      padding-bottom: calc(0.375em - 1px)
      padding-left: calc(0.625em - 1px)
      padding-right: calc(0.625em - 1px)
      padding-top: calc(0.375em - 1px)
      width: 100%
      line-height: 1.42857143
      font-size: 1rem
      &:focus
        box-shadow: inset 0 1px 2px rgba(10, 10, 10, 0.1)
      &:active 
        outline: none
    &__span
      background-color: #ffffff
      border: 1px solid #dbdbdb
      border-radius: 4px
      color: #363636
      padding-bottom: calc(0.375em - 1px)
      padding-left: calc(0.625em - 1px)
      padding-right: calc(0.625em - 1px)
      padding-top: calc(0.375em - 1px)
      line-height: 1.42857143
      font-size: 1rem
      &:focus
        box-shadow: inset 0 1px 2px rgba(10, 10, 10, 0.1)
      &:active 
        outline: none
  .allitems
    cursor: pointer
    border: 1px solid #dbdbdb
    max-height: 150px
    height: calc(100vh - 240px)
    overflow-y: scroll
    &__list
      padding: 6px
      transition: 0.3s
      &:hover
        background: rgba(50, 115, 220, 1)
        color: #ffffff
</style>
