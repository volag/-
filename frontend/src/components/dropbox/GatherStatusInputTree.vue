<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="gatherStatusTreeData"
    v-model="value">
  </a-tree-select>
</template>

<script>
export default {
  name: 'GatherStatusInputTree',
  data () {
    return {
      gatherStatusTreeData: [{value:"0",label:"未收款"},{value:"1",label:"已收款"}],
      value: undefined
    }
  },
  methods: {
    reset () {
      this.value = ''
    }
  },
  // mounted () {
  //   this.$get('dept').then((r) => {
  //     this.deptTreeData = r.data.rows.children
  //   })
  // },
  watch: {
    value (value) {
      this.$emit('change', value)
    }
  }
}
</script>
