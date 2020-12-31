<!-- 组件 - 基础表格 基于elementUI二次封装 -->
<template>
  <el-table
    :data="tableData"
    ref="ba se"
    v-loading="loading"
    :height="tableHeight"
    @row-dblclick="rowDblclick"
    @selection-change="selectionChange"
    @header-dragend="headerDragend"
    border
    :stripe="false"
    fit
    highlight-current-row
    tooltip-effect="dark"
    style="width: 100%"
  >
    <!-- 复选框 -->
    <template v-if="isSelection">
      <el-table-column type="selection" width="40"></el-table-column>
    </template>
    <!-- prop: 字段名name, label: 展示的名称, fixed: 是否需要固定(left, right), minWidth: 设置列的最小宽度(不传默认值),
		oper: 是否有操作列, oper.name: 操作列字段名称, oper.clickFun: 操作列点击事件, formatter: 格式化内容 -->
    <el-table-column
      v-for="(th, key) in columns"
      v-if="!th.hidden"
      :key="key"
      column-key="th.columnKey"
      :prop="th.prop"
      :label="th.label"
      :fixed="th.fixed || false"
      :width="th.width"
      :min-width="th.minWidth"
      :align="th.align || 'left'"
      :render-header="th.renderHeader"
      :sortable="th.sortable"
      :show-overflow-tooltip="th.showTooltip === false ? false : true"
      :class-name="th.className"
      :resizable="isResizable(th)"
    >
      <!-- 表头 -->
      <template slot="header" slot-scope="scope">
        <div class="cell-box">
          {{ scope.column.label }}
        </div>
      </template>
      <template slot-scope="scope">
        <!-- 操作列 oper表示是操作列，否则不是-->
        <template v-if="th.oper">
          <el-button
            v-for="(o, key) in th.oper"
            :key="key"
            :class="o.cls"
            :type="o.type"
            @click="o.clickFun && o.clickFun(scope.row, th, scope)"
            :disabled="operBtnDisabled(o, scope.row)"
            :icon="o.icon"
            size="mini"
          >
          </el-button>
        </template>
        <!-- slot列 自定义内容显示 需要在把slot设置为true，并且prop中传入slotName  -->
        <template v-else-if="th.slot">
          <slot :name="th.prop" :$index="scope.$index" :row="scope.row"></slot>
        </template>
        <!-- 个性化配置 tag标签 showType = tag -->
        <template v-else-if="th.showType === 'tag'">
          <el-tag 
            :type="getTagType(scope.row, th)">
              {{formatterCell(scope.row, th, scope)}}
          </el-tag>
        </template>
        <!--formatter：格式化内容-->
        <template v-else>
          <span v-html="formatterCell(scope.row, th, scope)"></span>
        </template>
      </template>
    </el-table-column>
    <!-- 表格内容为空 -->
    <template slot="empty">
      <div class="table-empty-box">
          <!-- 随意组装内容为空组件 -->
          <div>没有数据slot</div>
        </div>
      </div>
    </template>
  </el-table>
</template>

<script>
export default {
  name: "TableBase",
  props: {
    tableData: {
      // 表格数据
      type: Array,
      default: function () {
        return [];
      },
    },
    columns: {
      // 表格头部
      type: Array,
      default: function () {
        return [];
      },
    },
    tableHeight: {
      //高度
      type: String,
      default: "100%"
    },
    loading: {
      // 加载等待
      type: Boolean,
      default: false
    },
    isSelection: {
      // 是否展示复选框
      type: Boolean,
      default: false
    },
    rowDblclick: {
      // 单行双击事件
      type: Function,
      default: function (row, event, column) {
        console.log("双击--default: ");
      },
    },
  },
  updated: function () {
    // 更新视图
    this.$refs.baseTable.doLayout();
  },
  methods: {
    //格式化单元格内容
    formatterCell(row, th, scope) {
      var val = row[th.prop];
      val = val || val === 0 ? val : "";
      if (th.formatter && typeof th.formatter === "function") {
        return th.formatter(val, row, th, scope);
      }
      return val;
    },
    // 选中变化
    selectionChange(val) {
      this.$emit("selection-change", val);
    },
    //获取标签样式
    getTagType(row, th) {
      var cls = th.tagType || "";
      var val = row[th.prop] || "";
      if (cls && typeof cls === "function") {
        return cls(val, row, th);
      }
      return cls || "";
    },
    //按钮是否禁用
    operBtnDisabled(o, row) {
      if (o.disabled && typeof o.disabled === "function") {
        return o.disabled(row);
      }
      return !!o.disabled;
    },
    //拖动表头宽度
    headerDragend() {
      this.$refs.baseTable.doLayout();
    },
    //对应列是否可以通过拖动改变宽度
    isResizable(th) {
      //固定列、按钮列不可拖动
      return !(th.fixed || th.oper);
    },
  }
};
</script>
