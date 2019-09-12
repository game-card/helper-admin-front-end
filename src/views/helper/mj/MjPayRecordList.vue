<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="收入支出">
              <j-dict-select-tag placeholder="请选择收入支出" v-model="queryParam.payType" dictCode="pay_type"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="支付平台">
              <j-dict-select-tag placeholder="请选择支付平台" v-model="queryParam.fromAccountType" dictCode="account_type"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="支付帐号">
                <a-input placeholder="请输入支付帐号" v-model="queryParam.fromAccount"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="支付银行">
                <j-dict-select-tag placeholder="请选择支付银行" v-model="queryParam.fromBankType" dictCode="bank_type"/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="支付卡号">
                <a-input placeholder="请输入支付卡号" v-model="queryParam.fromBankAccount"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="接收平台">
                <j-dict-select-tag placeholder="请选择接收平台" v-model="queryParam.toAccountType" dictCode="account_type"/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="接收帐户">
                <a-input placeholder="请输入接收帐户" v-model="queryParam.toAccount"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="接收银行">
                <j-dict-select-tag placeholder="请选择接收银行" v-model="queryParam.toBankType" dictCode="bank_type"/>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="接收卡号">
                <a-input placeholder="请输入接收卡号" v-model="queryParam.toBankAccount"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="支付订单号">
                <a-input placeholder="请输入支付订单号" v-model="queryParam.payOrderId"></a-input>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->
    
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('支付记录')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">
        
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="图片不存在" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无此文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="uploadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <mjPayRecord-modal ref="modalForm" @ok="modalFormOk"></mjPayRecord-modal>
  </a-card>
</template>

<script>

  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import MjPayRecordModal from './modules/MjPayRecordModal'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
  import {initDictOptions, filterMultiDictText} from '@/components/dict/JDictSelectUtil'
  export default {
    name: "MjPayRecordList",
    mixins:[JeecgListMixin],
    components: {
      JDictSelectTag,
      MjPayRecordModal
    },
    data () {
      return {
        description: '支付记录管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'收入支出',
            align:"center",
            dataIndex: 'payType',
            customRender:(text)=>{
              if(!text){
                return ''
              }else{
                return filterMultiDictText(this.dictOptions['payType'], text+"")
              }
            }
          },
          {
            title:'支付用户',
            align:"center",
            dataIndex: 'fromUserId'
          },
          {
            title:'支付平台',
            align:"center",
            dataIndex: 'fromAccountType',
            customRender:(text)=>{
              if(!text){
                return ''
              }else{
                return filterMultiDictText(this.dictOptions['fromAccountType'], text+"")
              }
            }
          },
          {
            title:'支付帐号',
            align:"center",
            dataIndex: 'fromAccount'
          },
          {
            title:'支付银行',
            align:"center",
            dataIndex: 'fromBankType',
            customRender:(text)=>{
              if(!text){
                return ''
              }else{
                return filterMultiDictText(this.dictOptions['fromBankType'], text+"")
              }
            }
          },
          {
            title:'支付卡号',
            align:"center",
            dataIndex: 'fromBankAccount'
          },
          {
            title:'接收用户',
            align:"center",
            dataIndex: 'toUserId'
          },
          {
            title:'接收平台',
            align:"center",
            dataIndex: 'toAccountType',
            customRender:(text)=>{
              if(!text){
                return ''
              }else{
                return filterMultiDictText(this.dictOptions['toAccountType'], text+"")
              }
            }
          },
          {
            title:'接收帐户',
            align:"center",
            dataIndex: 'toAccount'
          },
          {
            title:'接收银行',
            align:"center",
            dataIndex: 'toBankType',
            customRender:(text)=>{
              if(!text){
                return ''
              }else{
                return filterMultiDictText(this.dictOptions['toBankType'], text+"")
              }
            }
          },
          {
            title:'接收卡号',
            align:"center",
            dataIndex: 'toBankAccount'
          },
          {
            title:'支付订单号',
            align:"center",
            dataIndex: 'payOrderId'
          },
          {
            title:'支付金额',
            align:"center",
            dataIndex: 'payNum'
          },
          {
            title:'支付时间',
            align:"center",
            dataIndex: 'payTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/helper.mj/mjPayRecord/list",
          delete: "/helper.mj/mjPayRecord/delete",
          deleteBatch: "/helper.mj/mjPayRecord/deleteBatch",
          exportXlsUrl: "/helper.mj/mjPayRecord/exportXls",
          importExcelUrl: "helper.mj/mjPayRecord/importExcel",
        },
        dictOptions:{
         payType:[],
         fromAccountType:[],
         fromBankType:[],
         toAccountType:[],
         toBankType:[],
        } 
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      initDictConfig(){
        initDictOptions('pay_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'payType', res.result)
          }
        })
        initDictOptions('account_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'fromAccountType', res.result)
          }
        })
        initDictOptions('bank_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'fromBankType', res.result)
          }
        })
        initDictOptions('account_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'toAccountType', res.result)
          }
        })
        initDictOptions('bank_type').then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'toBankType', res.result)
          }
        })
      }
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>