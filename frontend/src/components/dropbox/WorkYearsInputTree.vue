<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="workYearsTreeData"
    v-model="value"
    :defaultValue="message">
  </a-tree-select>
</template>

<script>
export default {
  props:["message"],
  name: 'WorkYearsInputTree',
  data () {
    return {
      workYearsTreeData: [{value:"1年",label:"1年"},
      {value:"1-3年",label:"1-3年"},
      {value:"3-5年",label:"3-5年"},
      {value:"5-10年",label:"5-10年"},
      {value:"10年以上",label:"10年以上"}
      ],
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
