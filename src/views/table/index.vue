<template>
  <div>
    <CustomTable v-bind="tableProps">
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
    this.tableProps.data = [
      {
        name: "张三",
        age: 18,
        gender: "男",
        companyName: "公司A",
        job: "开发",
        entryTime: "2021-01-01",
      },
      {
        name: "李花",
        age: 20,
        gender: "女",
        companyName: "公司B",
        job: "设计",
        entryTime: "2020-05-15",
      },
      {
        name: "王五",
        age: 22,
        gender: "男",
        companyName: "公司C",
        job: "测试",
        entryTime: "2019-03-10",
      },
      {
        name: "赵翠",
        age: 24,
        gender: "女",
        companyName: "公司D",
        job: "产品",
        entryTime: "2018-07-20",
      },
    ];
  },
  mounted() {
    setTimeout(() => {
      // this.tableProps.columns[2].prop = 'age'
    }, 2000);
  },
};
</script>

<style lang="scss" scoped></style>
