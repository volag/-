<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="salaryTreeData"
    v-model="value"
    :defaultValue="message"
  ></a-tree-select>
</template>

<script>
export default {
  props:["message"],
  name: "SalaryInputTree",
  data() {
    return {
      salaryTreeData: [
        { value: "10-15万", label: "10-15万" },
        { value: "15-20万", label: "15-20万" },
        { value: "20-30万", label: "20-30万" },
        { value: "30-50万", label: "30-50万" },
        { value: "50-100万", label: "50-100万" },
        { value: "100万以上", label: "50-100万以上" },
      ],
      value: undefined
    };
  },
  methods: {
    reset() {
      this.value = "";
    }
  },
  // mounted () {
  //   this.$get('dept').then((r) => {
  //     this.deptTreeData = r.data.rows.children
  //   })
  // },
  watch: {
    value(value) {
      this.$emit("change", value);
    }
  }
};
</script>
