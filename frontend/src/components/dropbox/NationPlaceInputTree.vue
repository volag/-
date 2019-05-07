<template>
  <a-select
      :size="size"
      defaultValue=""
      style="width: 350px;"
      @change="nationPlaceChange"
    >
      <a-select-option v-for="(item,index) of nationPlaceTreeData" :key="index">
        {{item}}
      </a-select-option>
    </a-select>
</template>

<script>
import axios from 'axios'

export default {
  name: 'NationPlaceInputTree',
  data () {
    return {
      nationPlaceTreeData: [],
      value: undefined
    }
  },
  methods: {
    reset () {
      this.value = ''
    },
    nationPlaceChange (value) {
      this.$emit("nationPlaceChange",`${this.nationPlaceTreeData[value].countyname}`)
    },
  },
  mounted () {
    axios.get('../../../static/file/city.json').then((r) => {
      this.nationPlaceTreeData = r.data
      console.log(this.nationPlaceTreeData)
    })
  },
  // watch: {
  //   value (value) {
  //     this.$emit('change', value)
  //   }
  // }
}
</script>
