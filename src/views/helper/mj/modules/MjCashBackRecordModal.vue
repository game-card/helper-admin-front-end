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

        <a-form-item label="申请用户" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'playerId', validatorRules.playerId]" placeholder="请输入申请用户"></a-input>
        </a-form-item>
          
        <a-form-item label="申请金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'totalNum', validatorRules.totalNum]" placeholder="请输入申请金额"></a-input>
        </a-form-item>
          
        <a-form-item label="返现状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-dict-select-tag type="list" v-decorator="['status']" :trigger-change="true" dictCode="cashback_status" placeholder="请选择返现状态"/>
        </a-form-item>
          
        <a-form-item label="执行金额" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'doneNum', validatorRules.doneNum]" placeholder="请输入执行金额"></a-input>
        </a-form-item>
          
        <a-form-item label="执行时间" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-date placeholder="请选择执行时间" v-decorator="[ 'doneTime', validatorRules.doneTime]" :trigger-change="true" :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" style="width: 100%"/>
        </a-form-item>
          
        <a-form-item label="支付记录" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input v-decorator="[ 'payRecordId', validatorRules.payRecordId]" placeholder="请输入支付记录"></a-input>
        </a-form-item>
          
        <a-form-item label="回执图片" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <j-upload v-decorator="['picturePath']" :trigger-change="true"></j-upload>
        </a-form-item>
          
        
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import JDate from '@/components/jeecg/JDate'  
  import JUpload from '@/components/jeecg/JUpload'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  
  export default {
    name: "MjCashBackRecordModal",
    components: { 
      JDate,
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
        playerId:{},
        totalNum:{},
        status:{},
        doneNum:{},
        doneTime:{},
        payRecordId:{},
        picturePath:{},
        },
        url: {
          add: "/helper.mj/mjCashBackRecord/add",
          edit: "/helper.mj/mjCashBackRecord/edit",
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
          this.form.setFieldsValue(pick(this.model,'playerId','totalNum','status','doneNum','doneTime','payRecordId','picturePath'))
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
        this.form.setFieldsValue(pick(row,'playerId','totalNum','status','doneNum','doneTime','payRecordId','picturePath'))
      }
      
    }
  }
</script>