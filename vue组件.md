## 1.二维码

### 注册组件

```vue
<template>
<van-overlay :show="show" @click="show = false">
    <div class="wrapper" @click.stop>
        <div class="block" >
            <qr-stream @decode="onDecode" @init="onInit" class="mb"> 
                <div style="color: red;" class="frame"></div>
    </qr-stream>
            <!-- <qr-capture @decode="onDecode" class="mb"> 
<div style="color: red;" class="frame"></div>
    </qr-capture>  -->
            <span>将二维码放入框内，即可自动扫描</span>
            {{result}}
    </div>
    </div>
    </van-overlay>

</template>

<script>
    // 引入
    import { 
        QrStream, 
        QrCapture, 
        QrDropzone,

    } from 'vue3-qr-reader';
    import { Overlay } from 'vant';

    export default {
        components: {
            QrStream,QrCapture,QrDropzone,
            [Overlay.name]: Overlay
        },
        name: 'Qrcode',
        props: {
            show: {
                type: Boolean,
                default: false
            },
        },
        data() {
            return {
                error: '' ,//错误信息

            }
        },
        methods: {
            onDecode(result) {
                this.result = result
                window.location.href = this.result
                this.$router.push(result)
            }
        }

    }
</script>
<style scoped>
    .wrapper {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100%;
    }

    .block {
        width: 100%;
        height: 100%;
        background-color: #fff;
    }
</style>
```

### 使用

```vue
<script>
    import Qrcode from '@/components/Qrcode.vue';
    export default {
        components {
        	'vant-qrcode': Qrcode,
    	},
        data() {
        	// 二维码
        	show: false
    	}
    },
</script>

<vant-qrcode :show=show/>
<vant-qrcode :show=show></vant-qrcode>
```

## 2. 表单组件

使用之前，在`main`中配置全局

```
// 自定义全局函数
import { FormModel } from 'ant-design-vue'

Vue.use(FormModel)
```

### 组件

基于 `Ant-designer-vue` 的组件

官网：https://2x.antdv.com/components/overview-cn/

```vue
<template>
<a-modal
         :title="formConfig.name"
         :width="720"
         :visible="visible"
         :confirmLoading="loading"
         @cancel="() => {cancel()}"
         @ok="() => {submit()}"
         v-bind="formItemLayoutWithOutLabel"
         >
    <a-spin :spinning="loading">
        <a-form-model ref="form" :model="dynamicValidateForm" class="ant-advanced-search-form">
            <a-row class="protolable">
                <a-col :span="12">
                    <span>协议名称</span>
    </a-col>
                <a-col :span="12">
                    <span>传输方向</span>
    </a-col>
    </a-row>
            <a-row :gutter="8" class="cells">
                <a-col :span="12">
                    <a-form-model-item
                                       v-bind="formItemLayout"
                                       prop="cellName"
                                       :rules="{
                                               required: true,
                                               message: '请输入协议名称',
                                               trigger: 'change',
                                               }">
                        <a-input
                                 placeholder="GPS传输协议"
                                 v-model="dynamicValidateForm.cellName"
                                 />
    </a-form-model-item>
    </a-col>
                <a-col :span="12">
                    <a-form-model-item
                                       v-bind="formItemLayout"
                                       prop="transferDirect"
                                       :rules="{
                                               required: true,
                                               message: '请选择传输方向',
                                               trigger: 'change',
                                               }">
                        <a-select v-model="dynamicValidateForm.transferDirect" style="width: 120px" @change="handleChange">
                            <a-select-option value=3>
                                通用
    </a-select-option>
                            <a-select-option value=1>
                                上行
    </a-select-option>
                            <a-select-option value=2>
                                下行
    </a-select-option>
                            <a-select-option value=4 disabled>
                                文件
    </a-select-option>
    </a-select>
    </a-form-model-item>
    </a-col>
    </a-row>

            <a-divider >标题</a-divider>

            <a-row class="protolable">
                <a-col :span="12">
                    <span>名字</span>
    </a-col>
                <a-col :span="12">
                    <span>类型</span>
    </a-col>
    </a-row>

            <div v-for="(domain, index) in dynamicValidateForm.domains" :key="domain.key" v-bind="index === 0 ? formItemLayout : {}">
                <a-row :gutter="8" class="cells">
                    <a-col :span="12">
                        <a-form-model-item
                                           :prop="'domains.' + index + '.paramNames'"
                                           :rules="{
                                                   required: true,
                                                   message: '请输入名字',
                                                   trigger: 'change',
                                                   }"
                                           >
                            <a-input placeholder="只限大小写" v-model="domain.paramNames"/>
    </a-form-model-item>
    </a-col>
                    <a-col :span="12">
                        <a-form-model-item
                                           :prop="'domains.' + index + '.paramTypes'"
                                           :rules="{
                                                   required: true,
                                                   message: '请选择类型',
                                                   trigger: 'change',
                                                   }"
                                           >
                            <a-select value="string" v-model="domain.paramTypes" style="width: 120px" @change="handleChange">
                                <a-select-option value="string">
                                    字符串
    </a-select-option>
                                <a-select-option value="char">
                                    字符型
    </a-select-option>
                                <a-select-option value="int">
                                    整型
    </a-select-option>
    </a-select>
    </a-form-model-item>
    </a-col>
                    <a-col>
                        <a-icon
                                v-if="dynamicValidateForm.domains.length > 1"
                                class="dynamic-delete-button"
                                type="minus-circle-o"
                                :disabled="dynamicValidateForm.domains.length === 1"
                                @click="removeDomain(domain)"
                                />
    </a-col>
    </a-row>
    </div>

            <a-row class="cells" style="margin-top: 20px">
                <a-col>
                    <a-form-model-item v-bind="formItemLayout">
                        <a-button type="dashed" style="width: 24%" @click="addDomain">
                            <a-icon type="plus" />添加
    </a-button>
    </a-form-model-item>
    </a-col>
    </a-row>

    </a-form-model>
    </a-spin>
    </a-modal>
</template>
<script>
    import pick from 'lodash.pick'

    export default {
        props: {
            visible: {
                type: Boolean,
                required: true
            },
            edit: {
                type: Boolean,
                required: true
            },
            model: {
                type: Object,
                default: () => null
            },
            loading: {
                type: Boolean,
                default: () => false
            },
            formConfig: {
                required: true,
                type: Object
            }

        },
        data() {
            this.formLayout = {
                labelCol: {
                    xs: { span: 24 },
                    sm: { span: 7 }
                },
                wrapperCol: {
                    xs: { span: 24 },
                    sm: { span: 13 }
                }
            }
            return {
                // form: this.$form.createForm(this),
                formItemLayout: {
                    labelCol: {
                        xs: { span: 24 },
                        sm: { span: 4 }
                    },
                    wrapperCol: {
                        xs: { span: 24 },
                        sm: { span: 20 }
                    }
                },
                formItemLayoutWithOutLabel: {
                    wrapperCol: {
                        xs: { span: 24, offset: 0 },
                        sm: { span: 20, offset: 4 }
                    }
                },
                dynamicValidateForm: {
                    cellType: '1',
                    transferDirect: '3',
                    domains: []
                }
            }
        },
        created() {
            // 防止 第二次打开 不会写入 默认值
            this.$watch('visible', () => {
                this.dynamicValidateForm = {
                    upThreshold: 0,
                    downThreshold: 0,
                    standard: '2'
                }
            })
            // 修改回显数据
            this.$watch('model', () => {
                if (this.model != null && this.edit) {
                    console.log(this.model)
                    var i = 1
                    this.model.paramList.map(e => {
                        this.dynamicValidateForm.domains.push({
                            paramNames: e.paramName,
                            paramTypes: e.paramType + '',
                            key: i
                        })
                        i += 1
                    })
                    this.dynamicValidateForm.cellName = this.model.cellName
                    this.dynamicValidateForm.transferDirect = this.model.transferDirect + ''
                }
            })
        },
        methods: {
            submit() {
                const form = this.dynamicValidateForm

                this.$refs['form'].validate(valid => {
                    if (valid) {
                        this.dynamicValidateForm = {
                            transferDirect: '3',
                            domains: []
                        }
                    }
                    this.$emit('submit', valid, form, this.edit)
                })
            },
            cancel() {
                this.dynamicValidateForm = {
                    transferDirect: '3',
                    domains: []
                }
                this.$emit('cancel')
            },
            removeDomain(item) {
                const index = this.dynamicValidateForm.domains.indexOf(item)
                if (index !== -1) {
                    this.dynamicValidateForm.domains.splice(index, 1)
                }
            },
            addDomain() {
                this.dynamicValidateForm.domains.push({
                    paramNames: '',
                    paramTypes: 'string',
                    key: Date.now()
                })
            }

        }
    }
</script>
<style>
    .ant-advanced-search-form .ant-form-item {
        margin-bottom: 0;
        background: #fbfbfb;
        /*border: 1px solid #d9d9d9;*/
        border-radius: 6px;
    }
    .ant-form-item-label > label {
        overflow: visible;
    }
    .ant-advanced-search-form .ant-form-item {
        display: flex;
    }

    .ant-advanced-search-form .ant-form-item-control-wrapper {
        flex: 1;
    }

    #components-form-demo-advanced-search .ant-form {
        max-width: none;
    }
    #components-form-demo-advanced-search .search-result-list {
        margin-top: 16px;
        border: 1px dashed #e9e9e9;
        border-radius: 6px;
        background-color: #fafafa;
        min-height: 200px;
        text-align: center;
        padding-top: 80px;
    }
    .protolable {
        line-height: 50px;
        font-weight: bold;
    }
    .cells {
        height: 40px;
    }
</style>
```

### 使用

```vue
<template>
<create-form
             :visible="visible"
             :form-config="peonForm"
             :loading="confirmLoading"
             :edit="edit"
             :model="mdl"
             @cancel="handleCancel"
             @submit="handleOk"
             />
</template>

<script>
    import CreateForm from './modules/CreateForm'

    export default {
        name: 'ProtoList',
        components: {
            CreateForm,
        },
        data () {
            return {
                peonForm: {
                    name: '创建协议'
                },
                // create model
                visible: false,
                edit: false,
                confirmLoading: false
            }
        },
        created () {
        },
        computed: {
        },
        methods: {
            /**
             * @param isPass true 为通过 false 为不通过
             * @param values values 为表单数据 其中 domains 项为 动态数组数据
             * @param edit true 为 修改 false 为 新增
             */
            handleOk(isPass, values, edit) {
                if (isPass) {
                    if (edit) {
                        console.log('修改')
                        // 修改 e.g.
                        new Promise((resolve, reject) => {
                            setTimeout(() => {
                                resolve()
                            }, 1000)
                        }).then(res => {
                            this.visible = false
                            this.confirmLoading = false
                            this.$refs.table.refresh()
                            this.$message.info('修改成功')
                        })
                    } else {
                        console.log('新增')
                        // 新增
                        new Promise((resolve, reject) => {
                            setTimeout(() => {
                                resolve()
                            }, 1000)
                        }).then(res => {
                            this.visible = false
                            this.confirmLoading = false
                            // 刷新表格
                            this.$refs.table.refresh()
                            // 调用自定义 请求参数，将包含数组的 json 对象 输出为 k-v 对象，满足接口要求
                            const requestParameters = this.requestArrayToJson(values)
                            request(requestParameters)
                                .then(res => {})
                            // 框架重置表单数据
                            // 不需要自己手动重置重置表单数据
                        })
                    }
                } else {
                    console.log('submit error')
                    this.confirmLoading = false
                }

            },
            handleCancel () {
                this.visible = false
            },
        }
    }
</script>
<style>

</style>
```

## 3. 表单文件上传

### 组件

```vue
<template>
  <a-modal
    :title="formConfig.name"
    :width="720"
    :visible="visible"
    :confirmLoading="loading"
    @cancel="() => {cancel()}"
    @ok="() => {submit()}"
    v-bind="formItemLayoutWithOutLabel"
  >
    <a-spin :spinning="loading">
      <a-form-model ref="form" :model="dynamicValidateForm" class="ant-advanced-search-form">
        <a-row class="protolable">
          <a-col :span="8">
            <span>类型</span>
          </a-col>
        </a-row>
        <a-row :gutter="8" class="cells">
          <a-col :span="8">
            <a-form-model-item
              v-bind="formItemLayout"
              prop="type"
              :rules="{
                  required: true,
                  message: '请输入节点类型',
                  trigger: 'change',
                }">
              <a-input v-model="dynamicValidateForm.type"/>
            </a-form-model-item>
          </a-col>
        </a-row>

        <a-divider >其他项</a-divider>

        <a-row :gutter="8">
          <a-col class="protolable" :span="4">
            <span>上传固件</span>
          </a-col>
          <a-col :span="10">
            <a-form-model-item
              v-bind="formItemLayout"
              >
              <a-upload
                action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
                list-type="picture-card"
                :file-list="fileList"
                @preview="handlePreview"
                :multiple="false"
                @change="handleImgChange">
                <div v-if="fileList.length < 1">
                  <a-icon type="plus" />
                  <div class="ant-upload-text">
                    Upload
                  </div>
                </div>
              </a-upload>
              <a-modal :visible="previewVisible" :footer="null" @cancel="handleCancel">
                <img alt="example" style="width: 100%" :src="previewImage" />
              </a-modal>
            </a-form-model-item>
          </a-col>
        </a-row>

      </a-form-model>
    </a-spin>
  </a-modal>

</template>

<script>

import { getNodeTypeList } from '@/api/node'

export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    edit: {
      type: Boolean,
      required: true
    },
    loading: {
      type: Boolean,
      default: () => false
    },
    formConfig: {
      required: true,
      type: Object
    },
    model: {
      type: Object,
      default: () => null
    }
  },
  data () {
    this.formLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      }
    }
    return {
      formItemLayout: {
        labelCol: {
          xs: { span: 24 },
          sm: { span: 4 }
        }
      },
      formItemLayoutWithOutLabel: {
        wrapperCol: {
          xs: { span: 24, offset: 0 },
          sm: { span: 20, offset: 4 }
        }
      },
      dynamicValidateForm: {
        file: null
      },
      nodeTypeList: [],
      previewVisible: false,
      previewImage: '',
      fileList: []
    }
  },
  created () {
    this.getNodeTypeList()
    // 监控用户 点击 新建或 编辑 触发默认值 键入
    this.$watch('visible', () => {
      this.dynamicValidateForm = {
        file: null
      }
    })
    // 监控 用户点击 编辑触发
    this.$watch('model', () => {
      if (this.model != null && this.edit) {
        this.dynamicValidateForm.type = this.model.type
        const imgUrl = this.model.img
        const imgName = imgUrl.substring(imgUrl.lastIndexOf('/') + 1)
        this.fileList.push(
          {
            uid: '-1',
            name: imgName,
            status: 'done',
            url: imgUrl
          }
        )
        this.urltoBase64(this.model.img, dataURL => {
          const file = this.base64toFile(dataURL)
          this.dynamicValidateForm.file = file
        })
      }
    })
  },
  methods: {
    handleCancel() {
      this.previewVisible = false
      this.fileList = []
    },
    async handlePreview(file) {
      if (!file.url && !file.preview) {
        file.preview = await this.getBase64(file.originFileObj)
      }
      this.previewImage = file.url || file.preview
      this.previewVisible = true
    },
    handleImgChange({ fileList }) {
      this.fileList = fileList
      this.dynamicValidateForm.file = fileList[0].originFileObj
      console.log(this.fileList[0].originFileObj)
    },
    getNodeTypeList() {
      getNodeTypeList().then(res => {
        if (res.status === '200') {
          this.nodeTypeList = res.data
        } else {
          this.$message.emitError(res.status)
        }
      })
    },
    submit() {
      // 暂存表单
      const form = this.dynamicValidateForm
      // 开放给外部 做校验，并提供表单数据、是新增还是编辑
      this.$refs['form'].validate(valid => {
        // 校验成功
        if (valid) {
          // 提交后 重置表单
          this.dynamicValidateForm = {
            file: null
          }
        }
        this.$emit('submit', valid, form, this.edit)
      })
    },
    cancel() {
      this.dynamicValidateForm = {
        file: null
      }
      this.fileList = []
      this.$emit('cancel')
    },
    handleChange() {
    }
  }
}
</script>
<style>
.ant-upload-select-picture-card i {
  font-size: 32px;
  color: #999;
}

.ant-upload-select-picture-card .ant-upload-text {
  margin-top: 8px;
  color: #666;
}
.ant-advanced-search-form {
  padding: 24px;
  background: #fbfbfb;
  border: 1px solid #d9d9d9;
  border-radius: 6px;
}
.ant-form-item-label > label {
  overflow: visible;
}
.ant-advanced-search-form .ant-form-item {
  display: flex;
}

.ant-advanced-search-form .ant-form-item-control-wrapper {
  flex: 1;
}

#components-form-demo-advanced-search .ant-form {
  max-width: none;
}
#components-form-demo-advanced-search .search-result-list {
  margin-top: 16px;
  border: 1px dashed #e9e9e9;
  border-radius: 6px;
  background-color: #fafafa;
  min-height: 200px;
  text-align: center;
  padding-top: 80px;
}
.protolable {
  line-height: 50px;
  font-weight: bold;
}
.cells {
  height: 40px;
}

.bigCells {
  height:  200px;
}

</style>
```

### 使用

```vue
<template>
<page-header-wrapper>
    <a-card :bordered="false">

        <div class="table-operator">
            <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
            <a-dropdown v-action:edit v-if="selectedRowKeys === null ? false : selectedRowKeys.length > 0">
                <a-menu slot="overlay">
                    <a-menu-item key="1"><a-icon type="delete" />删除</a-menu-item>
                    <a-menu-item key="2"><a-icon type="lock" />锁定</a-menu-item>
    </a-menu>
                <a-button style="margin-left: 8px">
                    批量操作 <a-icon type="down" />
    </a-button>
    </a-dropdown>
    </div>

        <!--:extra="converTo(item.standard)"-->
        <a-list :grid="{ gutter: 16, xs: 1, sm: 2, md: 4, lg: 4, xl: 6, xxl: 3 }" :data-source="columns">
            <a-list-item slot="renderItem" slot-scope="item">
                <a-card :title="item.title" type="inner">
                    <a slot="extra" href="#">{{ converTo(item.standard) }}</a>
                    <a-icon style="margin-left:5px" slot="extra" type="close" @click="delImg(item)"/>
                    <img
                         width="150"
                         height="150"
                         :src="item.img"
                         alt="logo"
                         @click="protoModify(item)"
                         />
    </a-card>
    </a-list-item>
    </a-list>

        <node-type-form
                        :formConfig="nodeTypeForm"
                        :visible="visible"
                        :model="mdl"
                        :edit="edit"
                        :loading="confirmLoading"
                        @cancel="handleCancel"
                        @submit="handleOk"
                        />

    </a-card>
    </page-header-wrapper>
</template>

<script>
    import { STable, Ellipsis } from '@/components'
    import { addNodeType, getNodeTypeList, updateNodeType, delNodeType } from '@/api/nodetype'
    import NodeTypeForm from './modules/NodeTypeForm'

    export default {
        name: 'ProtoList',
        components: {
            STable,
            Ellipsis,
            NodeTypeForm
        },
        data () {
            return {
                nodeTypeForm: {
                    name: '配置节点类型'
                },
                mdl: null,
                visible: false,
                edit: false,
                confirmLoading: false,
                columns: [],
                selectedRowKeys: [],
                selectedRows: []
            }
        },
        filters: {
        },
        created () {
            this.getNodeTypeList()
        },
        computed: {
        },
        methods: {
            delImg(record) {
                var thizz = this
                this.$confirm({
                    title: '想好了吗?',
                    content: '删除可能出现数据不可解析的情况!',
                    okText: '继续操作',
                    okType: 'danger',
                    cancelText: '取消',
                    onOk() {
                        const requestParam = { ntId: record.ntId }
                        del***(requestParam).then(res => {
                            const data = res.data
                            if (res.status === '200') {
                                if (data) {
                                    this.$message.info('删除成功')
                                }
                            } else {
                                this.$message.error(res.status)
                            }
                        })
                    },
                    onCancel() {
                    }
                })
            },
            getNodeTypeList() {
                handleOk(isPass, values, edit) {
                    this.confirmLoading = true
                    if (isPass) {
                        // 新增
                        if (!edit) {
                            new Promise((resolve, reject) => {
                                setTimeout(() => {
                                    resolve()
                                }, 1000)
                            }).then(res => {
                                this.visible = false
                                this.confirmLoading = false
                                // 刷新表格
                                //  新增
                                var thizz = this
                                add***(values).then(res => {
                                    thizz.getNodeTypeList()
                                    const data = res.data
                                    if (res.status === '200') {
                                        if (data) {
                                            this.$message.info('新增成功')
                                        }
                                    } else {
                                        this.$message.error(res.status)
                                    }
                                })
                            })
                        } else {
                            //  修改
                            new Promise((resolve, reject) => {
                                setTimeout(() => {
                                    resolve()
                                }, 1000)
                            }).then(res => {
                                this.visible = false
                                this.confirmLoading = false
                                // 刷新表格
                                // eslint-disable-next-line no-unreachable
                                // 待完成 节点新增
                                var thizz = this
                                // this.mdl 从  用户点击编辑后触发 protoModify方法执行后赋值
                                values.ntId = this.mdl.ntId
                                update***((values).then(res => {
                                    thizz.getNodeTypeList()
                                    const data = res.data
                                    if (res.status === '200') {
                                        if (data) {
                                            this.$message.info('修改成功')
                                        }
                                    } else {
                                        this.$message.error(res.status)
                                    }
                                })
                                          })
                        }
                    } else {
                        console.log('submit error')
                        this.confirmLoading = false
                    }
                },
                    handleCancel() {
                        this.visible = false
                    },
                        handleAdd () {
                            this.edit = false
                            this.mdl = null
                            this.visible = true
                        },
                            protoModify (record) {
                                this.mdl = record
                                this.edit = true
                                this.visible = true
                            },
                                handleEdit (record) {
                                    this.visible = true
                                }
            }
        }
    }
</script>
<style>
    .rowClass {
        color: blue;
    }
</style>

```



