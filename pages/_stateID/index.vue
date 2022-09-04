<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <v-expansion-panels v-model="panelList" multiple>
          <v-expansion-panel
            v-for="(panelItem, panelIndex) in panelItems"
            :key="`panel-${panelIndex}`"
            class="pa-0 transparent elevation-0"
          >
            <v-expansion-panel-header class="px-0">
              <p class="mb-0 text-16">{{ panelItem.name }}</p>
            </v-expansion-panel-header>
            <v-expansion-panel-content class="px-0 pb-2">
              <v-list-item-group v-model="listSelected[panelIndex]">
                <v-list-item
                  v-for="(item, i) in panelItem.items"
                  :key="`${item.panelIndex}__${i}`"
                  class="text-decoration-none footer-link-hover white--text text-14 px-0"
                  dense
                  @click="handleClick(panelIndex, i)"
                >
                  <v-list-item-content>
                    {{ item }}
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'StatePage',
  data() {
    return {
      panelList: [],
      panelItems: [
        {
          name: 'Panel 1',
          items: ['Item 1', 'Item 2', 'Item 3', 'Item 4', 'Item 5'],
        },
        {
          name: 'Panel 2',
           items: ['Item 1', 'Item 2', 'Item 3', 'Item 4', 'Item 5'],
        },
        {
          name: 'Panel 3',
          items: ['Item 1', 'Item 2', 'Item 3', 'Item 4', 'Item 5'],
        },
        {
          name: 'Panel 4',
           items: ['Item 1', 'Item 2', 'Item 3', 'Item 4', 'Item 5'],
        },
      ],
      tempList: {},
      listSelected: [],
    }
  },
  mounted() {
    this.handleURL()
  },
  methods: {
    handleURL() {
      const filterKey = Object.keys(this.panelItems)
      const query = this.$route.query
      const ListFilterVal = (query.value || '').split(',')
      let binaryFilter = this.$route.params.stateID
      let keyNum = 0
      filterKey.forEach((item, index) => {
        const key = filterKey.length - (index + 1)
        const val = ListFilterVal.length - keyNum - 1
        const binaryState = binaryFilter >= Math.pow(2, key)

        if (binaryState) {
          keyNum++
          binaryFilter -= Math.pow(2, key)
          this.panelList.push(key)
          this.listSelected[filterKey[key]] = isNaN(
            parseInt(ListFilterVal[val])
          )
            ? false
            : parseInt(ListFilterVal[val])
          this.tempList[filterKey[key]] = parseInt(ListFilterVal[val])
        }
      })
    },
    handleClick(panelState, val) {
    
      if (this.tempList[panelState]) {
        if (this.tempList[panelState] === val) {
          delete this.tempList[panelState]
        } else {
          this.tempList[panelState] = val
        }
      } else if (this.tempList[panelState] === val) {
        Object.keys(this.sgcitemData).forEach((item, key) => {
          if (item === panelState) {
            this.sgcPanelList.splice(this.sgcPanelList.indexOf(key), 1)
          }
        })

        delete this.tempList[panelState]
      } else {
        this.tempList[panelState] = val
      }
      this.history()
    },
    history() {
      let num = 0
      let index = 0
      const listVal = Object.keys(this.panelItems).reduce((res, item) => {
        if (Object.keys(this.tempList).includes(item)) {
          const sellStatus = Math.pow(2, index)
          num += sellStatus
          res.push(this.tempList[item])
        }
        index++
        return res
      }, [])
      const query = {
        value: listVal.toString(),
      }

      window.history.pushState(
        { ...query },
        null,
        `/${num}?value=${query.value}`
      )
    },
  },
}
</script>
