<template>
  <div>
    <!-- 工具栏 支持插槽 toolbarLeft toolbarRight -->
    <section class="table__toolbar">
      <div class="toolbar__left">
        <slot name="toolbarLeft" />
      </div>
      <div class="toolbar__right">
        <slot name="toolbarRight" />
      </div>
    </section>
    <!-- 表格主体 -->
    <section class="table__body">
      <el-table ref="tableRef" v-bind="$attrs" v-on="$listeners" :border="border">
        <!-- el-table的empty插槽， 表格空数据时显示的内容， -->
        <template #empty>
          <slot name="empty" />
        </template>
        <!-- el-table的append插槽， 表格底部追加的内容， -->
        <template #append>
          <slot name="append" />
        </template>
        <!-- 递归组件：table-column -->
        <table-column
          v-for="(col, index) in columns"
          :key="col.key || `table-column-${col.prop || index}`"
          :column="col"
        >
        </table-column>
      </el-table>
    </section>
    <!-- 表格底部 分页 支持插槽 footer -->
    <section class="table__footer">
      <slot name="footer">
        <el-pagination
          v-if="pagination"
          :current-page="pageNumber"
          :page-size="pageSize"
          :page-sizes="[10, 30, 50, 100]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
          @size-change="$_handleSizeChange"
          @current-change="$_handleCurrentChange"
        />
      </slot>
    </section>
  </div>
</template>

<script>
export default {
  inheritAttrs: false,
  provide() {
    return {
      tableSlots: this.$scopedSlots, // 提供当前组件的插槽给子组件使用
    }
  },
  components: {
    TableColumn: () => import("./components/TableColumn.vue"),
  },
  props: {
    border: {
      type: Boolean,
      default: true,
    },
    /**
     * 表格列配置，列的每一项在el-table-column的基础上扩展了一些属性，具体如下：
     * const columns = [
     *   {
     *     ... // el-table-column的属性
     *     key?: 唯一标识
     *     slot?: 插槽名称
     *     headerSlot?: 表头插槽名称
     *     render?: 自定义渲染函数
     *     children?: 多级表头
     *   }
     * ]
     */
    columns: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * 是否开启分页
     */
    pagination: {
      type: Boolean,
      default: false,
    },
    /**
     * 分页大小, 支持 .sync 修饰符
     */
    pageSize: {
      type: Number,
      default: 10,
    },
    /**
     * 分页页码, 支持 .sync 修饰符
     */
    pageNumber: {
      type: Number,
      default: 1,
    },
    /**
     * 表格数据总条数
     */
    total: {
      type: Number,
      default: 0,
    },
  },
  methods: {
    /**
     * 获取表格的ref引用
     */
    getTableRef() {
      return this.$refs.tableRef;
    },
    /**
     * 分页大小改变
     * @param {Number} pageSize 每页显示条
     */
    $_handleSizeChange(pageSize) {
      this.$emit("update:pageSize", pageSize);
      // 重置页码为 1
      this.$emit("update:pageNumber", 1);
      this.$emit("page-change", { pageNumber: 1, pageSize });
    },
    /**
     * 分页页码改变
     * @param {Number} pageNumber 当前页码
     */
    $_handleCurrentChange(pageNumber) {
      this.$emit("update:pageNumber", pageNumber);
      this.$emit("page-change", { pageNumber });
    },
  },
};
</script>

<style lang="scss" scoped>
.table__toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}
.table__body {
  margin-bottom: 8px;
}
</style>
