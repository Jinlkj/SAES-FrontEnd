<script>
import { ElMessage, ElMessageBox } from 'element-plus'
import {reactive, ref} from 'vue'
import axios from "axios";
import useClipboard from "vue-clipboard3";
const { toClipboard } = useClipboard();

export default {
  setup(){
    const res=ref('')
    const copy=() =>{
      let resValue = result.value.join(',');
      res.value = resValue;
      console.log("{}",res)
      toClipboard(res.value)
      ElMessage({
        message:"复制成功",
        type:"success"
      })
    }

    const result= ref([])
    const form = reactive({
      method:'',
      key1:'',
      key2:'',
      module:'false',
      plainText:'',
      initVector:'',
    })
    const dataVisible=ref(true)
    const isBinaryString=(input)=> {
      // 创建一个正则表达式，它匹配只包含0和1的字符串
      const binaryPattern = /^[01]+$/;

      // 使用正则表达式的test方法检查输入字符串
      return binaryPattern.test(input);
    }
    const onSubmit = () => {
      const submitForm=ref({
        plainText:"",
        plainTexts:[],
        cypherTexts:[],
        key1:"",
        key2:"",
        cypherText:"",
        operation:"OP",
        initVector:""
      });
      if(form.method){
        if(form.method!='EncryptText' && form.module){
          ElMessageBox.alert('CBC模式暂未开放', '提示', {
            autofocus: false,
            type:'success',
            confirmButtonText: '确认',
          })
        }else
          if((form.method=='EncryptNormal' || form.method=='EncryptText') && form.key1!=''){
            if(form.key1.length!=16){
              ElMessageBox.alert('请输入16位密钥', '错误', {
                autofocus: false,
                type:'error',
                confirmButtonText: '确认',
              })
            }else if(!isBinaryString(form.key1)){
              ElMessageBox.alert('密钥只能包含0/1', '错误', {
                autofocus: false,
                type:'error',
                confirmButtonText: '确认',
              })
            }else{
              submitForm.value.initVector=form.initVector
              submitForm.value.plainText=form.plainText
              submitForm.value.key1=form.key1
              if(form.module){
                axios.post("http://localhost:8080/EncryptWordsCBC",submitForm.value).then((response)=>{
                  if(response.data.code==1){
                    dataVisible.value=false
                    result.value=response.data.data.cypherTexts
                    console.log(result.value)
                  }else{
                    ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }
                })
              }else{
                if(form.method=='EncryptText'){
                  axios.post("http://localhost:8080/EncryptWords",submitForm.value).then((response)=>{
                    if(response.data.code==1){
                      dataVisible.value=false
                      result.value=response.data.data.cypherTexts
                      console.log(result.value)
                    }else{
                      ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                        autofocus: false,
                        type:'error',
                        confirmButtonText: '确认',
                      })
                    }
                  })
                }else{
                  if(form.plainText.length!=16){
                    ElMessageBox.alert('请输入16位明文', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }else if(!isBinaryString(form.plainText)){
                    ElMessageBox.alert('明文只能包含0/1', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }else {
                    axios.post("http://localhost:8080/SAESEncrypt",submitForm.value).then((response)=>{
                      if(response.data.code==1){
                        dataVisible.value=false
                        result.value=[response.data.data.cypherText]
                        console.log(result.value)
                      }else{
                        ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                          autofocus: false,
                          type:'error',
                          confirmButtonText: '确认',
                        })
                      }
                    })
                  }
                }
              }
            }

          } else if((form.method=='DoubleEncrypt' || form.method=='TrippleEncrypt') && form.key1!='' && form.key2!=''){
            if(form.plainText.length!=16){
              ElMessageBox.alert('请输入16位明文', '错误', {
                autofocus: false,
                type:'error',
                confirmButtonText: '确认',
              })
            }else if(!isBinaryString(form.plainText)){
              ElMessageBox.alert('明文只能包含0/1', '错误', {
                autofocus: false,
                type:'error',
                confirmButtonText: '确认',
              })
            }else {
              submitForm.value.key1=form.key1
              submitForm.value.key2=form.key2
              submitForm.value.plainText=form.plainText
              if(form.method=='DoubleEncrypt'){
                axios.post("http://localhost:8080/DoubleSAESEncrypt",submitForm.value).then((response)=>{
                  if(response.data.code==1){
                    dataVisible.value=false
                    result.value=[response.data.data.cypherText]
                    console.log(result.value)
                  }else{
                    ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }
                })
              }else{
                axios.post("http://localhost:8080/TrippleSAESEncrypt",submitForm.value).then((response)=>{
                  if(response.data.code==1){
                    dataVisible.value=false
                    result.value=[response.data.data.cypherText]
                    console.log(result.value)
                  }else{
                    ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }
                })
              }
            }
          }else{
            ElMessageBox.alert('请补全密钥', '错误', {
              autofocus: false,
              type:'error',
              confirmButtonText: '确认',
            })
          }
      }else{
        ElMessageBox.alert('请输入加密方式', '错误', {
          autofocus: false,
          type:'warning',
          confirmButtonText: '确认',
        })
      }
    }
    return{
      onSubmit,
      form,
      dataVisible,
      result,
      copy
    }
    console.log(form.module)
  }
}
</script>

<template>
  <el-form :model="form" label-width="120px" :label-position="'top'" :size="'large'">
    <el-form-item label="加密方式">
      <el-select v-model="form.method" placeholder="选择你的加密方式">
        <el-option label="加密文字（长文本）" value="EncryptText" />
        <el-option label="加密明文（16位明文）" value="EncryptNormal" />
        <el-option label="双重加密（16位明文）" value="DoubleEncrypt" />
        <el-option label="三重加密（16位明文）" value="TrippleEncrypt" />
      </el-select>
    </el-form-item>
    <div v-if="form.method=='EncryptNormal' || form.method=='EncryptText'">
      <el-form-item label="密钥">
        <el-input v-model="form.key1" placeholder="请输入密钥"/>
      </el-form-item>
    </div>
    <div v-else>
      <el-form-item label="密钥1">
        <el-input v-model="form.key1" placeholder="请输入密钥"/>
      </el-form-item>
      <el-form-item label="密钥2">
        <el-input v-model="form.key2" placeholder="请输入密钥"/>
      </el-form-item>
    </div>
    <el-form-item label="CBC模式">
      <el-switch v-model="form.module" />
    </el-form-item>
    <div v-if="form.module">
      <el-form-item label="初始向量">
        <el-input v-model="form.initVector" placeholder="请输入向量"/>
      </el-form-item>
    </div>
    <div v-if="form.method=='EncryptText'">
      <el-form-item label="加密文字">
        <el-input v-model="form.plainText" type="textarea" />
      </el-form-item>
    </div>
    <div v-else>
      <el-form-item label="加密明文">
        <el-input v-model="form.plainText" placeholder="请输入需要加密的明文"/>
      </el-form-item>
    </div>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">加密</el-button>
    </el-form-item>

  </el-form>
  <div v-if="dataVisible">
    <el-empty description="没有数据" />
  </div>
  <div v-else>
    <el-card class="box-card" style="width: 100% ">
      <template #header>
        <div class="card-header">
          <span><el-icon><List /></el-icon></span>
          <el-button type="primary" @click="copy">
            <el-icon><CopyDocument /></el-icon>
          </el-button>
        </div>
      </template>
      <div>
        <div v-for="index in result" :key="index" class="text item">{{index }}</div>
      </div>
    </el-card>
  </div>

</template>

<style>
.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.box-card {
  width: 480px;
}
</style>