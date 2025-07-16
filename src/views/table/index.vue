<template>
  <div>
    <CustomTable
      :pageNumber.sync="tableProps.pageNumber"
      :pageSize.sync="tableProps.pageSize"
      v-bind="tableProps"
      @page-change="onPageChange"
    >
      <template #nameHeader="{ column }">
        <span>{{ column.label }}-自定义表头</span>
      </template>
      <template #jobHeader="{ column }">
        <span>{{ column.label }}-多级表头自定义</span>
      </template>
      <template #companyName="{ row, column, $index }">
        <span>{{ `${row[column.property]}-${$index}` }}</span>
      </template>
    </CustomTable>
  </div>
</template>

<script>
export default {
  name: "Table",
  components: {
    CustomTable: () => import("@/components/CustomTable"),
  },
  data() {
    return {
      tableProps: {
        ref: "customTable",
        loading: false,
        pagination: true, // 开启分页
        pageNumber: 1,
        pageSize: 10,
        total: 0, // 表格数组总数
        maxHeight: '500px',
        data: [],
        columns: [
          {
            label: "序号",
            type: "index",
            width: "60px",
          },
          {
            type: "selection",
            width: "44px",
          },
          {
            label: "姓名",
            prop: "name",
            headerSlot: "nameHeader", // 表头插槽
          },
          {
            label: "性别",
            prop: "gender",
            // 列 render函数写法
            render: (h, { row }) => {
              return h(
                "el-tag",
                {
                  props: {
                    type: row.gender === "男" ? "" : "danger",
                  },
                },
                row.gender
              );
            },
          },
          {
            label: "工作信息",
            minWidth: "200px",
            // 多级表头
            children: [
              {
                label: "公司名称",
                prop: "companyName",
                slot: "companyName", // 列插槽名称
              },
              {
                label: "职位",
                prop: "job",
                headerSlot: "jobHeader", // 表头插槽名称
              },
              {
                label: "入职时间",
                prop: "entryTime",
                // 表头格式化
                formatter: (row, column, cellValue) => {
                  return `【${cellValue}】`;
                },
              },
            ],
          },
          {
            label: "操作",
            prop: "action",
            render: (h, { row, column, $index }) => {
              return h("span", [
                h(
                  "el-button",
                  {
                    props: { type: "text" },
                    on: {
                      click: () => {
                        console.log("表格ref", this.$refs.customTable.getTableRef());
                        console.log("编辑", row, column, $index);
                      },
                    },
                  },
                  "编辑"
                ),
                h(
                  "el-button",
                  {
                    props: { type: "text", link: true },
                    on: {
                      click: () => {
                        console.log("删除", row, column, $index);
                      },
                    },
                  },
                  "删除"
                ),
              ]);
            },
          },
        ],
      },
    };
  },
  created() {
    this.fetchTableData();
  },
  methods: {
    /**
     * 表格分页变化
     */
    onPageChange() {
      this.fetchTableData()
    },
    /**
     * 获取表格数据
     */
    async fetchTableData() {
      try {
        this.tableProps.loading =true
        const { pageNumber, pageSize } = this.tableProps
        const reqData = {
          pageNumber,
          pageSize
        }
        const { total, rows } = await this.getList(reqData)
        this.tableProps.data = rows
        this.tableProps.total = total
      }catch(err) {
        console.error(err)
      }finally{
        this.tableProps.loading = false
      }
    },
    /**
     * 模拟接口请求
     * @param data 请求参数
     */
    getList(data) {
      console.log('请求参数', data)
      const { pageSize } = data
      const temp = Array.from({ length: pageSize }).map((item, index) => {
        return {
          name: `姓名${index + 1}`,
          age: 20 + index,
          gender: index % 2 === 0 ? "男" : "女",
          companyName: `公司${String.fromCharCode(65 + (index % 4))}`,
          job: ["开发", "设计", "测试", "产品"][index % 4],
          entryTime: `202${index % 3}-0${(index % 3) + 1}-01`,
        };
      })
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve({
            total: 100,
            rows: temp
          });
        }, 1200)
      })
    }
  },
};
</script>

<style lang="scss" scoped></style>
