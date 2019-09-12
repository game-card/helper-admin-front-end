<template>
  <a-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">

        <a-form-item label="收入支出" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['payType']" :trigger-change="true" dictCode="pay_type" placeholder="请选择收入支出"/>
        </a-form-item>
          
        <a-form-item label="支付用户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'fromUserId', validatorRules.fromUserId]" placeholder="请输入支付用户"></a-input>
        </a-form-item>
          
        <a-form-item label="支付平台" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['fromAccountType']" :trigger-change="true" dictCode="account_type" placeholder="请选择支付平台"/>
        </a-form-item>
          
        <a-form-item label="支付帐号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'fromAccount', validatorRules.fromAccount]" placeholder="请输入支付帐号"></a-input>
        </a-form-item>
          
        <a-form-item label="支付银行" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['fromBankType']" :trigger-change="true" dictCode="bank_type" placeholder="请选择支付银行"/>
        </a-form-item>
          
        <a-form-item label="支付卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'fromBankAccount', validatorRules.fromBankAccount]" placeholder="请输入支付卡号"></a-input>
        </a-form-item>
          
        <a-form-item label="接收用户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'toUserId', validatorRules.toUserId]" placeholder="请输入接收用户"></a-input>
        </a-form-item>
          
        <a-form-item label="接收平台" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['toAccountType']" :trigger-change="true" dictCode="account_type" placeholder="请选择接收平台"/>
        </a-form-item>
          
        <a-form-item label="接收帐户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'toAccount', validatorRules.toAccount]" placeholder="请输入接收帐户"></a-input>
        </a-form-item>
          
        <a-form-item label="接收银行" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['toBankType']" :trigger-change="true" dictCode="bank_type" placeholder="请选择接收银行"/>
        </a-form-item>
          
        <a-form-item label="接收卡号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'toBankAccount', validatorRules.toBankAccount]" placeholder="请输入接收卡号"></a-input>
        </a-form-item>
          
        <a-form-item label="支付订单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payOrderId', validatorRules.payOrderId]" placeholder="请输入支付订单号"></a-input>
        </a-form-item>
          
        <a-form-item label="支付金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payNum', validatorRules.payNum]" placeholder="请输入支付金额"></a-input>
        </a-form-item>
          
        <a-form-item label="支付时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payTime', validatorRules.payTime]" placeholder="请输入支付时间"></a-input>
        </a-form-item>
          
        
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "MjPayRecordModal",
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
        payType:{},
        fromUserId:{},
        fromAccountType:{},
        fromAccount:{},
        fromBankType:{},
        fromBankAccount:{},
        toUserId:{},
        toAccountType:{},
        toAccount:{},
        toBankType:{},
        toBankAccount:{},
        payOrderId:{},
        payNum:{},
        payTime:{},
        },
        url: {
          add: "/helper.mj/mjPayRecord/add",
          edit: "/helper.mj/mjPayRecord/edit",
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
          this.form.setFieldsValue(pick(this.model,'payType','fromUserId','fromAccountType','fromAccount','fromBankType','fromBankAccount','toUserId','toAccountType','toAccount','toBankType','toBankAccount','payOrderId','payNum','payTime'))
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
        this.form.setFieldsValue(pick(row,'payType','fromUserId','fromAccountType','fromAccount','fromBankType','fromBankAccount','toUserId','toAccountType','toAccount','toBankType','toBankAccount','payOrderId','payNum','payTime'))
      }
      
    }
  }
</script>