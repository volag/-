<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="educationBackgroundTreeData"
    v-model="value"
    :defaultValue="message">
  </a-tree-select>
</template>

<script>
export default {
  props:["message"],
  name: 'EducationBackgroundInputTree',
  data () {
    return {
      educationBackgroundTreeData: [
      {value:"中专",label:"中专"},
      {value:"大专",label:"大专"},
      {value:"本科",label:"本科"},
      {value:"硕士",label:"硕士"},
      {value:"博士",label:"博士"}
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
