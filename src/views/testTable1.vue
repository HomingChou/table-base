<template>
  <div>
    <table-base
      :table-data="tableData"
      :columns="tableColumns"
      :loading="isLoading"
      :isSelection="true"
    >
      <!-- 自定义slot -->
      <template slot="slotIsPause" slot-scope="scope">
          <el-switch
            v-model="scope.row.isPause"
            :active-value="1"
            :inactive-value="0"
            size="mini"
            @change="statusChange"
          >
          </el-switch>
      </template>
    </table-base>
  </div>
</template>

<script>
import TableBase from "../components/TableBase"

export default {
  name: "TestTable1",
  components: {
    TableBase,
  },
  data() {
    return {
      isLoading: false,
      tableData: [
        {
          id: 1001,
          name: "测试名称1",
          status: 1,
          validDate: new Date(),
          remark: "备注信息1",
          isPause: 1
        },
        {
          id: 1002,
          name: "测试名称2",
          status: 2,
          validDate: new Date(),
          remark: "备注信息2",
          isPause: 0
        },
        {
          id: 1003,
          name: "测试名称3",
          status: 3,
          validDate: new Date(),
          remark: "备注信息3",
          isPause: 1
        },
        {
          id: 1004,
          name: "测试名称4",
          status: 4,
          validDate: new Date(),
          remark: "备注信息4",
          isPause: 0
        },
        {
          id: 1005,
          name: "测试名称5",
          status: 5,
          validDate: new Date(),
          remark: "备注信息5",
          isPause: 1
        },
      ],
      tableColumns: [
        { prop: "id", label: "编号", width: 150 },
        { prop: "name", label: "名称", width: 150 },
        {
          prop: "status",
          label: "状态",
          width: 150,
          showType: "tag",
          tagType(val, row, th) {
            let type = "";
            switch (val) {
              case 1:
                type = "info";
                break;
              case 2:
                type = "primary";
                break;
              case 3:
                type = "warning";
                break;
              case 4:
                type = "success";
                break;
              case 5:
                type = "danger";
                break;
              default:
                type = "info";
            }
            return type;
          },
          formatter(val, row, th) {
            let name = "";
            switch (val) {
              case 1:
                name = "未处理";
                break;
              case 2:
                name = "处理中";
                break;
              case 3:
                name = "中断";
                break;
              case 4:
                name = "成功";
                break;
              case 5:
                name = "失败";
                break;
              default:
                name = "未处理";
            }
            return name;
          },
        },
        {
          prop: "validDate",
          label: "生效时间",
          width: 150,
          formatter(val) {
            return val ? new Date(val).toLocaleDateString() : "";
          },
        },
        { prop: "remark", label: "备注", minWidth: 150 },
        {prop: 'slotIsPause', label: '是否通过', width: 120, slot: true, showTooltip: false},
        {
          prop: "oper",
          label: "操作",
          fixed: "right",
          width: 200,
          align: "center",
          oper: [
            {
              name: "编辑",
              icon: "el-icon-edit",
              type: "primary",
              clickFun: this.handleEdit,
            },
            {
              name: "删除",
              icon: "el-icon-delete",
              type: "danger",
              clickFun: this.handleDel,
            },
          ],
        },
      ],
    };
  },
  methods: {
    handleDel(row) {
      console.log("delete");
    },
    handleEdit(row) {
      console.log("edit");
    },
    statusChange() {
      console.log('change');
    }
  },
};
</script>

<style>
</style>
