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

        <a-form-item label="平台名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['accountType']" :trigger-change="true" dictCode="account_type" placeholder="请选择平台名称"/>
        </a-form-item>
          
        <a-form-item label="帐号" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'accountNum', validatorRules.accountNum]" placeholder="请输入帐号"></a-input>
        </a-form-item>
          
        <a-form-item label="帐号状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['status']" :trigger-change="true" dictCode="account_status" placeholder="请选择帐号状态"/>
        </a-form-item>
          
        <a-form-item label="所有人名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'ownerName', validatorRules.ownerName]" placeholder="请输入所有人名称"></a-input>
        </a-form-item>
          
        <a-form-item label="所有人电话" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'ownerMobile', validatorRules.ownerMobile]" placeholder="请输入所有人电话"></a-input>
        </a-form-item>
          
        <a-form-item label="收款二维码" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-upload v-decorator="['qrCodeImage']" :trigger-change="true"></j-upload>
        </a-form-item>
          
        
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import JUpload from '@/components/jeecg/JUpload'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "MjAccountModal",
    components: { 
      JUpload,
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
        accountType:{},
        accountNum:{},
        status:{},
        ownerName:{},
        ownerMobile:{},
        qrCodeImage:{},
        },
        url: {
          add: "/helper.mj/mjAccount/add",
          edit: "/helper.mj/mjAccount/edit",
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
          this.form.setFieldsValue(pick(this.model,'accountType','accountNum','status','ownerName','ownerMobile','qrCodeImage'))
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
        this.form.setFieldsValue(pick(row,'accountType','accountNum','status','ownerName','ownerMobile','qrCodeImage'))
      }
      
    }
  }
</script>