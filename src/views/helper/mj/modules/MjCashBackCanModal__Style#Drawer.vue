<template>
  <a-drawer
    :title="title"
    :width="width"
    placement="right"
    :closable="false"
    @close="close"
    :visible="visible">
  
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="支付记录" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payRecordId', validatorRules.payRecordId]" placeholder="请输入支付记录"></a-input>
        </a-form-item>
        <a-form-item label="玩家ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'playerId', validatorRules.playerId]" placeholder="请输入玩家ID"></a-input>
        </a-form-item>
        <a-form-item label="可返金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cashBackNum', validatorRules.cashBackNum]" placeholder="请输入可返金额"></a-input>
        </a-form-item>
        <a-form-item label="可返状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['status']" :trigger-change="true" dictCode="can_cashback_status" placeholder="请选择可返状态"/>
        </a-form-item>
        <a-form-item label="一级下线ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'followerDirectId', validatorRules.followerDirectId]" placeholder="请输入一级下线ID"></a-input>
        </a-form-item>
        <a-form-item label="一级下线" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'followerDirect', validatorRules.followerDirect]" placeholder="请输入一级下线"></a-input>
        </a-form-item>
        <a-form-item label="二级下线ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'followerIndirectId', validatorRules.followerIndirectId]" placeholder="请输入二级下线ID"></a-input>
        </a-form-item>
        <a-form-item label="二级下线" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'followerIndirect', validatorRules.followerIndirect]" placeholder="请输入二级下线"></a-input>
        </a-form-item>
        <a-form-item label="返现ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'cashBackId', validatorRules.cashBackId]" placeholder="请输入返现ID"></a-input>
        </a-form-item>
        
      </a-form>
    </a-spin>
    <a-button type="primary" @click="handleOk">确定</a-button>
    <a-button type="primary" @click="handleCancel">取消</a-button>
  </a-drawer>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "MjCashBackCanModal",
    components: { 
      JDictSelectTag,
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        validatorRules:{
        payRecordId:{},
        playerId:{},
        cashBackNum:{},
        status:{},
        followerDirectId:{},
        followerDirect:{},
        followerIndirectId:{},
        followerIndirect:{},
        cashBackId:{},
        },
        url: {
          add: "/helper.mj/mjCashBackCan/add",
          edit: "/helper.mj/mjCashBackCan/edit",
        }
     
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'payRecordId','playerId','cashBackNum','status','followerDirectId','followerDirect','followerIndirectId','followerIndirect','cashBackId'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
         
        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'payRecordId','playerId','cashBackNum','status','followerDirectId','followerDirect','followerIndirectId','followerIndirect','cashBackId'))
      }
      
    }
  }
</script>

<style lang="less" scoped>
/** Button按钮间距 */
  .ant-btn {
    margin-left: 30px;
    margin-bottom: 30px;
    float: right;
  }
</style>