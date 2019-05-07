<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="personSortTreeData"
    v-model="value">
  </a-tree-select>
</template>

<script>
export default {
  name: 'PersonSortInputTree',
  data () {
    return {
      personSortTreeData: [{value:"客户联系人",label:"客户联系人"},{value:"候选人",label:"候选人"}],
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
