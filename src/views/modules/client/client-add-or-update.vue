<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="终端名称" prop="clientName">
      <el-input v-model="dataForm.clientName" placeholder="终端名称"></el-input>
    </el-form-item>
    <el-form-item label="连接时的密钥" prop="clientKey">
      <el-input v-model="dataForm.clientKey" placeholder="连接时的密钥"></el-input>
    </el-form-item>
    <el-form-item label="颁发密钥" prop="securityKey">
      <el-input v-model="dataForm.securityKey" placeholder="安全密钥"></el-input>
    </el-form-item>
    <el-form-item label="客户端的类型" prop="clientType">
      <el-input v-model="dataForm.clientType" placeholder="客户端的类型"></el-input>
    </el-form-item>
    <el-form-item label="终端状态" prop="clientStatus">
      <el-input v-model="dataForm.clientStatus" placeholder="终端状态"></el-input>
    </el-form-item>
    <el-form-item label="终端的版本号" prop="clientVersion">
      <el-input v-model="dataForm.clientVersion" placeholder="终端的版本号"></el-input>
    </el-form-item>
    <el-form-item label="该终端的文件存储目录" prop="clientStorageDir">
      <el-input v-model="dataForm.clientStorageDir" placeholder="该终端的文件存储目录"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createDate">
      <el-input v-model="dataForm.createDate" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="memo">
      <el-input v-model="dataForm.memo" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          clientName: '',
          clientKey: '',
          clientType: '',
          clientStatus: '',
          clientVersion: '',
          clientStorageDir: '',
          createDate: '',
          securityKey: '',
          memo: ''
        },
        dataRule: {
          clientName: [
            { required: true, message: '终端名称不能为空', trigger: 'blur' }
          ],
          clientKey: [
            { required: true, message: '连接时的密钥不能为空', trigger: 'blur' }
          ],
          clientType: [
            { required: true, message: '客户端的类型不能为空', trigger: 'blur' }
          ],
          clientStatus: [
            { required: true, message: '终端状态不能为空', trigger: 'blur' }
          ],
          clientVersion: [
            { required: true, message: '终端的版本号不能为空', trigger: 'blur' }
          ],
          clientStorageDir: [
            { required: true, message: '该终端的文件存储目录不能为空', trigger: 'blur' }
          ],
          createDate: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/fts/client/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.clientName = data.client.clientName
                this.dataForm.clientKey = data.client.clientKey
                this.dataForm.clientType = data.client.clientType
                this.dataForm.clientStatus = data.client.clientStatus
                this.dataForm.clientVersion = data.client.clientVersion
                this.dataForm.clientStorageDir = data.client.clientStorageDir
                this.dataForm.createDate = data.client.createDate
                this.dataForm.memo = data.client.memo
                this.dataForm.securityKey = data.client.securityKey
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/fts/client/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'clientName': this.dataForm.clientName,
                'clientKey': this.dataForm.clientKey,
                'clientType': this.dataForm.clientType,
                'clientStatus': this.dataForm.clientStatus,
                'clientVersion': this.dataForm.clientVersion,
                'clientStorageDir': this.dataForm.clientStorageDir,
                'createDate': this.dataForm.createDate,
                'memo': this.dataForm.memo,
                'securityKey': this.dataForm.securityKey
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
