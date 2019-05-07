<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="projectStatusTreeData"
    v-model="value"
  ></a-tree-select>
</template>

<script>
export default {
  name: "ProjectStatusInputTree",
  data() {
    return {
      projectStatusTreeData: [
        { value: "0", label: "进行中"},
        { value: "1", label: "完成"},
        { value: "2", label: "暂停"},
        { value: "3", label: "终止"},
        { value: "4", label: "未开始"}
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
