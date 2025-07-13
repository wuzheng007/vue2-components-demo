<script>
export default {
  name: "TableColumn",
  inheritAttrs: false,
  props: {
    // 列配置
    column: {
      type: Object,
      required: true,
    },
    // 插槽
    scopedSlots: {
      type: Object,
      default: () => ({}),
    },
  },
  methods: {
    /**
     * 渲染多级表头列
     * @param h
     */
    renderMultiHeaderColumn(h) {
      const { column, scopedSlots } = this;
      return h(
        "el-table-column",
        {
          props: { align: "center", ...column },
          scopedSlots: {
            header: (scope) => this.renderHeader(column, scope),
          },
        },
        column.children.map((child, i) =>
          h("TableColumn", {
            key: child.key || `table-column-${child.prop || i}`,
            props: { column: child, scopedSlots },
          })
        )
      );
    },
    /**
     * 渲染单列
     * @param h
     */
    renderSingleColumn(h) {
      const { column } = this;
      return ["index", "selection"].includes(column.type)
        ? this.renderSpecialColumn(h)
        : this.renderNormalColumn(h);
    },
    /**
     * 渲染特殊列
     * @param h
     */
    renderSpecialColumn(h) {
      return h("el-table-column", {
        props: { align: "center", ...this.column },
      });
    },
    /**
     * 渲染普通列
     * @param h
     */
    renderNormalColumn(h) {
      return h("el-table-column", {
        props: { align: "center", ...this.column },
        scopedSlots: {
          header: (scope) => this.renderHeader(this.column, scope),
          default: (scope) => this.renderCell(h, scope),
        },
      });
    },
    /**
     * 渲染表头
     * @param {Object} col 列配置
     * @param {Object} scope 表头作用域
     */
    renderHeader(col, { column, $index }) {
      const name = col.headerSlot;
      if (name) {
        // 使用表头插槽
        return this.scopedSlots[name]?.({ column, $index }) || column.label;
      } else {
        // 默认表头渲染
        return column.label;
      }
    },
    /**
     * 渲染单元格
     * @param h
     * @param scope
     */
    renderCell(h, scope) {
      const { row, column, $index } = scope;
      if (this.column.render) {
        // 使用render函数渲染
        return this.column.render(h, { row, column, $index });
      } else if (this.column.slot) {
        // 使用插槽渲染
        return this.scopedSlots[this.column.slot]?.({
          row,
          column,
          $index,
        });
      } else {
        if (this.column.formatter) {
          // 使用formatter函数格式化
          return this.column.formatter(
            row,
            column,
            row[this.column.prop],
            $index
          );
        } else {
          // 默认渲染
          return row[this.column.prop] ?? "-";
        }
      }
    },
  },
  render(h) {
    const col = this.column;
    if (col.children?.length) {
      return this.renderMultiHeaderColumn(h);
    } else {
      return this.renderSingleColumn(h);
    }
  },
};
</script>
