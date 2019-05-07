<template>
  <a-tree-select
    :allowClear="true"
    :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
    :treeData="positionSortTreeData"
    v-model="value"
    :defaultValue="message"
  ></a-tree-select>
</template>

<script>
export default {
  props:["message"],
  name: "PositionSortInputTree",
  data() {
    return {
      positionSortTreeData: [
        { value: "猎头职位", label: "猎头职位" },
        { value: "企业职位", label: "企业职位" },
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
