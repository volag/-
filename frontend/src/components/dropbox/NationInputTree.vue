<template>
  <!-- <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="nationTreeData"
    v-model="value">
  </a-tree-select> -->
  <a-select
      :size="size"
      defaultValue=""
      style="width: 350px;"
      @change="nationChange"
    >
      <a-select-option v-for="(item,index) of nationTreeData" :key="index">
        {{item}}
      </a-select-option>
    </a-select>
</template>

<script>
import axios from 'axios'

export default {
  name: 'NationInputTree',
  data () {
    return {
      nationTreeData: [],
      value: undefined,
      size: 'default',
    }
  },
  methods: {
    reset () {
      this.value = ''
    },
    nationChange (value) {
      this.$emit("nationChange",`${this.nationTreeData[value]}`)
    },
  },
  mounted () {
    axios.get('../../../static/file/nation.json').then((r) => {
      this.nationTreeData = r.data
    })
  },
  // watch: {
  //   value (value) {
  //     this.$emit('change', value)
  //   }
  // }
}
</script>
