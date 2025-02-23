<template>
  <div class="app-container">
    <el-form :model="queryParams" ref="queryForm" :inline="true" v-show="showSearch" label-width="68px">
      <el-form-item label="边设备" prop="dId">
        <el-input
          v-model="queryParams.dId"
          placeholder="请输入边设备"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="应用ID" prop="appId">
        <el-input
          v-model="queryParams.appId"
          placeholder="请输入应用ID"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="设备唯一标识" prop="nodeId">
        <el-input
          v-model="queryParams.nodeId"
          placeholder="请输入设备唯一标识"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="设备名称" prop="nodeName">
        <el-input
          v-model="queryParams.nodeName"
          placeholder="请输入设备名称"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="子设备标识" prop="deviceId">
        <el-input
          v-model="queryParams.deviceId"
          placeholder="请输入子设备标识"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="厂商ID" prop="manufacturerId">
        <el-input
          v-model="queryParams.manufacturerId"
          placeholder="请输入厂商ID"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="设备型号" prop="model">
        <el-input
          v-model="queryParams.model"
          placeholder="请输入设备型号"
          clearable
          size="small"
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>
      <el-form-item label="连接状态" prop="connectStatus">
        <el-select v-model="queryParams.connectStatus" placeholder="请选择连接状态" clearable size="small">
          <el-option
            v-for="dict in dict.type.link_device_connect_status"
            :key="dict.value"
            :label="dict.label"
            :value="dict.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="是否支持设备影子" prop="shadowEnable">
        <el-select v-model="queryParams.shadowEnable" placeholder="请选择是否支持设备影子" clearable size="small">
          <el-option
            v-for="dict in dict.type.link_deviceInfo_shadow_enable"
            :key="dict.value"
            :label="dict.label"
            :value="dict.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-select v-model="queryParams.status" placeholder="请选择状态" clearable size="small">
          <el-option
            v-for="dict in dict.type.business_data_status"
            :key="dict.value"
            :label="dict.label"
            :value="dict.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="el-icon-search" size="mini" @click="handleQuery">搜索</el-button>
        <el-button icon="el-icon-refresh" size="mini" @click="resetQuery">重置</el-button>
      </el-form-item>
    </el-form>

    <el-row :gutter="10" class="mb8">
      <el-col :span="1.5">
        <el-button
          type="primary"
          plain
          icon="el-icon-plus"
          size="mini"
          @click="handleAdd"
          v-hasPermi="['link:deviceInfo:add']"
        >新增</el-button>
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="success"
          plain
          icon="el-icon-edit"
          size="mini"
          :disabled="single"
          @click="handleUpdate"
          v-hasPermi="['link:deviceInfo:edit']"
        >修改</el-button>
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="danger"
          plain
          icon="el-icon-delete"
          size="mini"
          :disabled="multiple"
          @click="handleDelete"
          v-hasPermi="['link:deviceInfo:remove']"
        >删除</el-button>
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="warning"
          plain
          icon="el-icon-download"
          size="mini"
          @click="handleExport"
          v-hasPermi="['link:deviceInfo:export']"
        >导出</el-button>
      </el-col>
      <right-toolbar :showSearch.sync="showSearch" @queryTable="getList"></right-toolbar>
    </el-row>

    <el-table v-loading="loading" :data="deviceInfoList" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55" align="center" />
      <el-table-column label="主键" align="center" prop="id" />
      <el-table-column label="边设备唯一标识" align="center" prop="edgeDevicesIdentification" />
      <el-table-column label="应用ID" align="center" prop="appId" />
      <el-table-column label="设备唯一标识" align="center" prop="nodeId" />
      <el-table-column label="设备名称" align="center" prop="nodeName" />
      <el-table-column label="子设备标识" align="center" prop="deviceId" width="200px"/>
      <el-table-column label="设备描述" align="center" prop="description" />
      <el-table-column label="厂商ID" align="center" prop="manufacturerId" />
      <el-table-column label="设备型号" align="center" prop="model" />
      <el-table-column label="连接状态" align="center" prop="connectStatus">
        <template slot-scope="scope">
          <dict-tag :options="dict.type.link_device_connect_status" :value="scope.row.connectStatus"/>
        </template>
      </el-table-column>
      <el-table-column label="是否支持设备影子" align="center" prop="shadowEnable">
        <template slot-scope="scope">
          <dict-tag :options="dict.type.link_deviceInfo_shadow_enable" :value="scope.row.shadowEnable"/>
        </template>
      </el-table-column>
      <el-table-column label="状态" align="center" prop="status">
        <template slot-scope="scope">
          <dict-tag :options="dict.type.business_data_status" :value="scope.row.status"/>
        </template>
      </el-table-column>
      <el-table-column label="创建者" align="center" prop="createBy" />
      <el-table-column label="创建时间" align="center" prop="createTime" width="180">
        <template slot-scope="scope">
          <span>{{
              parseTime(scope.row.createTime, "{y}-{m}-{d} {h}:{i}:{s}")
            }}</span>
        </template>
      </el-table-column>
      <el-table-column label="更新者" align="center" prop="updateBy" />
      <el-table-column label="更新时间" align="center" prop="updateTime" width="180">
        <template slot-scope="scope">
          <span>{{
              parseTime(scope.row.updateTime, "{y}-{m}-{d} {h}:{i}:{s}")
            }}</span>
        </template>
      </el-table-column>
      <el-table-column label="备注" align="center" prop="remark" />
      <el-table-column fixed="right" label="操作" align="center" width="200">
        <template slot-scope="scope">
          <el-tooltip class="item" effect="light" content="修改" placement="top">
            <el-button circle size="mini" type="primary" icon="el-icon-edit" @click="handleUpdate(scope.row)"
                       v-hasPermi="['link:deviceInfo:edit']">
            </el-button>
          </el-tooltip>
          <el-tooltip class="item" effect="light" content="删除" placement="top">
            <el-button circle size="mini" type="primary" icon="el-icon-delete" @click="handleDelete(scope.row)"
                       v-hasPermi="['link:deviceInfo:remove']">
            </el-button>
          </el-tooltip>
          <el-tooltip class="item" effect="light" content="设备影子" placement="top">
            <el-button circle size="mini" type="primary" icon="el-icon-s-operation"></el-button>
          </el-tooltip>
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total>0"
      :total="total"
      :page.sync="queryParams.pageNum"
      :limit.sync="queryParams.pageSize"
      @pagination="getList"
    />

    <!-- 添加或修改子设备管理对话框 -->
    <el-dialog :title="title" :visible.sync="open" width="500px" append-to-body>
      <el-form ref="form" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="边设备" prop="dId">
          <el-input v-model="form.dId" placeholder="请输入边设备" />
        </el-form-item>
        <el-form-item label="应用ID" prop="appId">
          <el-input v-model="form.appId" placeholder="请输入应用ID" />
        </el-form-item>
        <el-form-item label="设备唯一标识" prop="nodeId">
          <el-input v-model="form.nodeId" placeholder="请输入设备唯一标识" />
        </el-form-item>
        <el-form-item label="设备名称" prop="nodeName">
          <el-input v-model="form.nodeName" placeholder="请输入设备名称" />
        </el-form-item>
        <el-form-item label="子设备标识" prop="deviceId">
          <el-input v-model="form.deviceId" placeholder="请输入子设备标识" />
        </el-form-item>
        <el-form-item label="设备描述" prop="description">
          <el-input v-model="form.description" placeholder="请输入设备描述" />
        </el-form-item>
        <el-form-item label="厂商ID" prop="manufacturerId">
          <el-input v-model="form.manufacturerId" placeholder="请输入厂商ID" />
        </el-form-item>
        <el-form-item label="设备型号" prop="model">
          <el-input v-model="form.model" placeholder="请输入设备型号" />
        </el-form-item>
        <el-form-item label="连接状态" prop="connectStatus">
          <el-select v-model="form.connectStatus" placeholder="请选择连接状态">
            <el-option
              v-for="dict in dict.type.link_device_connect_status"
              :key="dict.value"
              :label="dict.label"
              :value="dict.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否支持设备影子" prop="shadowEnable">
          <el-select v-model="form.shadowEnable" placeholder="请选择是否支持设备影子">
            <el-option
              v-for="dict in dict.type.link_deviceInfo_shadow_enable"
              :key="dict.value"
              :label="dict.label"
              :value="parseInt(dict.value)"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="设备影子数据表名" prop="shadowTableName">
          <el-input v-model="form.shadowTableName" placeholder="请输入设备影子数据表名" />
        </el-form-item>
        <el-form-item label="状态" prop="status">
          <el-select v-model="form.status" placeholder="请选择状态(字典值：0启用  1停用)">
            <el-option
              v-for="dict in dict.type.business_data_status"
              :key="dict.value"
              :label="dict.label"
              :value="dict.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="备注" prop="remark">
          <el-input v-model="form.remark" type="textarea" placeholder="请输入内容" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitForm">确 定</el-button>
        <el-button @click="cancel">取 消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { listDeviceInfo, getDeviceInfo, delDeviceInfo, addDeviceInfo, updateDeviceInfo } from "@/api/link/deviceInfo";

export default {
  name: "DeviceInfo",
  dicts: ['link_device_connect_status', 'link_deviceInfo_shadow_enable', 'business_data_status'],
  data() {
    return {
      // 遮罩层
      loading: true,
      // 选中数组
      ids: [],
      // 非单个禁用
      single: true,
      // 非多个禁用
      multiple: true,
      // 显示搜索条件
      showSearch: true,
      // 总条数
      total: 0,
      // 子设备管理表格数据
      deviceInfoList: [],
      // 弹出层标题
      title: "",
      // 是否显示弹出层
      open: false,
      // 查询参数
      queryParams: {
        pageNum: 1,
        pageSize: 10,
        dId: null,
        appId: null,
        nodeId: null,
        nodeName: null,
        deviceId: null,
        description: null,
        manufacturerId: null,
        model: null,
        connectStatus: null,
        shadowEnable: null,
        shadowTableName: null,
        status: null,
      },
      // 表单参数
      form: {},
      // 表单校验
      rules: {
        dId: [
          { required: true, message: "边设备不能为空", trigger: "blur" }
        ],
        appId: [
          { required: true, message: "应用ID不能为空", trigger: "blur" }
        ],
        status: [
          { required: true, message: "状态(字典值：0启用  1停用)不能为空", trigger: "change" }
        ],
      }
    };
  },
  created() {
    this.getList();
  },
  methods: {
    /** 查询子设备管理列表 */
    getList() {
      this.loading = true;
      listDeviceInfo(this.queryParams).then(response => {
        this.deviceInfoList = response.rows;
        this.total = response.total;
        this.loading = false;
      });
    },
    // 取消按钮
    cancel() {
      this.open = false;
      this.reset();
    },
    // 表单重置
    reset() {
      this.form = {
        id: null,
        dId: null,
        appId: null,
        nodeId: null,
        nodeName: null,
        deviceId: null,
        description: null,
        manufacturerId: null,
        model: null,
        connectStatus: null,
        shadowEnable: null,
        shadowTableName: null,
        status: null,
        createBy: null,
        createTime: null,
        updateBy: null,
        updateTime: null,
        remark: null
      };
      this.resetForm("form");
    },
    /** 搜索按钮操作 */
    handleQuery() {
      this.queryParams.pageNum = 1;
      this.getList();
    },
    /** 重置按钮操作 */
    resetQuery() {
      this.resetForm("queryForm");
      this.handleQuery();
    },
    // 多选框选中数据
    handleSelectionChange(selection) {
      this.ids = selection.map(item => item.id)
      this.single = selection.length!==1
      this.multiple = !selection.length
    },
    /** 新增按钮操作 */
    handleAdd() {
      this.reset();
      this.open = true;
      this.title = "添加子设备管理";
    },
    /** 修改按钮操作 */
    handleUpdate(row) {
      this.reset();
      const id = row.id || this.ids
      getDeviceInfo(id).then(response => {
        this.form = response.data;
        this.open = true;
        this.title = "修改子设备管理";
      });
    },
    /** 提交按钮 */
    submitForm() {
      this.$refs["form"].validate(valid => {
        if (valid) {
          if (this.form.id != null) {
            updateDeviceInfo(this.form).then(response => {
              this.$modal.msgSuccess("修改成功");
              this.open = false;
              this.getList();
            });
          } else {
            addDeviceInfo(this.form).then(response => {
              this.$modal.msgSuccess("新增成功");
              this.open = false;
              this.getList();
            });
          }
        }
      });
    },
    /** 删除按钮操作 */
    handleDelete(row) {
      const ids = row.id || this.ids;
      this.$modal.confirm('是否确认删除子设备管理编号为"' + ids + '"的数据项？').then(function() {
        return delDeviceInfo(ids);
      }).then(() => {
        this.getList();
        this.$modal.msgSuccess("删除成功");
      }).catch(() => {});
    },
    /** 导出按钮操作 */
    handleExport() {
      this.download('link/deviceInfo/export', {
        ...this.queryParams
      }, `link_deviceInfo.xlsx`)
    }
  }
};
</script>
