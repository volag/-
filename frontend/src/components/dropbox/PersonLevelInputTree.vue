<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="personLevelTreeData"
    v-model="value">
  </a-tree-select>
</template>

<script>
export default {
  name: 'PersonLevelInputTree',
  data () {
    return {
      personLevelTreeData: [{value:"普通",label:"普通"},{value:"主管",label:"主管"},{value:"经理",label:"经理"}],
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
