<template>
  <a-modal
    :title="title"
    :width="1200"
    :visible="visible"
    :maskClosable="false"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel">
    <a-spin :spinning="confirmLoading">
      <!-- 主表单区域 -->
      <a-form :form="form">
        <a-row>

          <a-col :span="12">
            <a-form-item label="昵称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="[ 'name', validatorRules.name]" placeholder="请输入昵称"></a-input>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="性别" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['sex']" :trigger-change="true" dictCode="sex" placeholder="请选择性别"/>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="手机号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="[ 'mobile', validatorRules.mobile]" placeholder="请输入手机号码"></a-input>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="用户状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['status']" :trigger-change="true" dictCode="user_status" placeholder="请选择用户状态"/>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="推广码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="[ 'promoCode', validatorRules.promoCode]" placeholder="请输入推广码"></a-input>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="会员级别" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['lever']" :trigger-change="true" dictCode="account_lever" placeholder="请选择会员级别"/>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="上级" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="[ 'superior', validatorRules.superior]" placeholder="请输入上级"></a-input>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="通知方式" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['noticeWay']" :trigger-change="true" dictCode="notice_type" placeholder="请选择通知方式"/>
            </a-form-item>
          </a-col>
        
          <a-col :span="12">
            <a-form-item label="可返次数" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="[ 'canCashbackTime', validatorRules.canCashbackTime]" placeholder="请输入可返次数"></a-input>
            </a-form-item>
          </a-col>
        
        </a-row>
      </a-form>

      <!-- 子表单区域 -->
      <a-tabs v-model="activeKey" @change="handleChangeTabs">
        <a-tab-pane tab="玩家帐号" :key="refKeys[0]" :forceRender="true">
          <j-editable-table
            :ref="refKeys[0]"
            :loading="mjPlayerAccountTable.loading"
            :columns="mjPlayerAccountTable.columns"
            :dataSource="mjPlayerAccountTable.dataSource"
            :maxHeight="300"
            :rowNumber="true"
            :rowSelection="true"
            :actionButton="true"/>
        </a-tab-pane>
        
        <a-tab-pane tab="玩家银行卡" :key="refKeys[1]" :forceRender="true">
          <j-editable-table
            :ref="refKeys[1]"
            :loading="mjPlayerCardTable.loading"
            :columns="mjPlayerCardTable.columns"
            :dataSource="mjPlayerCardTable.dataSource"
            :maxHeight="300"
            :rowNumber="true"
            :rowSelection="true"
            :actionButton="true"/>
        </a-tab-pane>
        
        <a-tab-pane tab="可用游戏" :key="refKeys[2]" :forceRender="true">
          <j-editable-table
            :ref="refKeys[2]"
            :loading="mjPlayerGameTable.loading"
            :columns="mjPlayerGameTable.columns"
            :dataSource="mjPlayerGameTable.dataSource"
            :maxHeight="300"
            :rowNumber="true"
            :rowSelection="true"
            :actionButton="true"/>
        </a-tab-pane>
        
      </a-tabs>

    </a-spin>
  </a-modal>
</template>

<script>

  import pick from 'lodash.pick'
  import { FormTypes,getRefPromise } from '@/utils/JEditableTableUtil'
  import { JEditableTableMixin } from '@/mixins/JEditableTableMixin'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  import {initDictOptions, filterMultiDictText} from '@/components/dict/JDictSelectUtil'

  export default {
    name: 'MjPlayerModal',
    mixins: [JEditableTableMixin],
    components: {
      JDictSelectTag,
    },
    data() {
      return {
        labelCol: {
          span: 6
        },
        wrapperCol: {
          span: 16
        },
        labelCol2: {
          span: 3
        },
        wrapperCol2: {
          span: 20
        },
        // 新增时子表默认添加几行空数据
        addDefaultRowNum: 0,
        validatorRules: {
          name:{},
          sex:{},
          mobile:{},
          status:{},
          promoCode:{},
          lever:{},
          superior:{},
          noticeWay:{},
          canCashbackTime:{},
        },
        refKeys: ['mjPlayerAccount', 'mjPlayerCard', 'mjPlayerGame', ],
        tableKeys:['mjPlayerAccount', 'mjPlayerCard', 'mjPlayerGame', ],
        activeKey: 'mjPlayerAccount',
        // 玩家帐号
        mjPlayerAccountTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '帐户类型',
              key: 'accountType',
              type: FormTypes.select,
              dictCode: "account_type",
              placeholder: '请输入${title}',
            },
            {
              title: '帐号',
              key: 'accountNum',
              type: FormTypes.input,
              defaultValue: '',
              placeholder: '请输入${title}',
            },
            {
              title: '状态',
              key: 'status',
              type: FormTypes.select,
              dictCode: "account_status",
              placeholder: '请输入${title}',
            },
            {
              title: '收款二维码',
              key: 'qrCodeImage',
              type: FormTypes.upload,
              defaultValue: '',
              placeholder: '点击上传',
            },
          ]
        },
        // 玩家银行卡
        mjPlayerCardTable: {
          loading: false,
          dataSource: [],
          columns: [
            // {
            //   title: '银行类型',
            //   key: 'bankType',
            //   type: FormTypes.input,
            //   defaultValue: '',
            //   placeholder: '请输入${title}',
            // },
            {
              title:'银行类型',
              align:"center",
              key: 'bankType',
              type: FormTypes.select,
              dictCode: "bank_type",
              placeholder: '请输入${title}',
            },
            {
              title: '银行卡号码',
              key: 'cardNum',
              type: FormTypes.input,
              defaultValue: '',
              placeholder: '请输入${title}',
            },
            {
              title: '状态',
              key: 'status',
              type: FormTypes.select,
              dictCode: "account_status",
              placeholder: '请输入${title}',
            },
            {
              title: '收款二维码',
              key: 'qrCodeImage',
              type: FormTypes.upload,
              defaultValue: '',
              placeholder: '点击上传',
            },
          ]
        },
        // 可用游戏
        mjPlayerGameTable: {
          loading: false,
          dataSource: [],
          columns: [
            {
              title: '玩家ID',
              key: 'playerId',
              type: FormTypes.input,
              defaultValue: '',
              placeholder: '请输入${title}',
            },
            {
              title: '游戏ID',
              key: 'gameId',
              type: FormTypes.input,
              defaultValue: '',
              placeholder: '请输入${title}',
            },
          ]
        },
        url: {
          add: "/helper.mj/mjPlayer/add",
          edit: "/helper.mj/mjPlayer/edit",
          mjPlayerAccount: {
            list: '/helper.mj/mjPlayer/queryMjPlayerAccountByMainId'
          },
          mjPlayerCard: {
            list: '/helper.mj/mjPlayer/queryMjPlayerCardByMainId'
          },
          mjPlayerGame: {
            list: '/helper.mj/mjPlayer/queryMjPlayerGameByMainId'
          },
        dictOptions:{
          bankType:[],
          status:[],
          }
        }
      }
    },
    methods: {
      initDictConfig(){
        initDictOptions('account_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'accountType', res.result)
          }
        })
        initDictOptions('bank_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'bankType', res.result)
          }
        })
        initDictOptions('account_status').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'status', res.result)
          }
        })
      },

      getAllTable() {
        let values = this.tableKeys.map(key => getRefPromise(this, key))
        return Promise.all(values)
      },
      /** 调用完edit()方法之后会自动调用此方法 */
      editAfter() {
        let fieldval = pick(this.model,'name','sex','mobile','status','promoCode','lever','superior','noticeWay','canCashbackTime')
        this.$nextTick(() => {
          this.form.setFieldsValue(fieldval)
        })
        // 加载子表数据
        if (this.model.id) {
          let params = { id: this.model.id }
          this.requestSubTableData(this.url.mjPlayerAccount.list, params, this.mjPlayerAccountTable)
          this.requestSubTableData(this.url.mjPlayerCard.list, params, this.mjPlayerCardTable)
          this.requestSubTableData(this.url.mjPlayerGame.list, params, this.mjPlayerGameTable)
        }
      },
      /** 整理成formData */
      classifyIntoFormData(allValues) {
        let main = Object.assign(this.model, allValues.formValue)

        return {
          ...main, // 展开
          mjPlayerAccountList: allValues.tablesValue[0].values,
          mjPlayerCardList: allValues.tablesValue[1].values,
          mjPlayerGameList: allValues.tablesValue[2].values,
        }
      },
      validateError(msg){
        this.$message.error(msg)
      }
      
      
    }
  }
</script>

<style scoped>
</style>