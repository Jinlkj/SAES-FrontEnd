<script>
import { ElMessage, ElMessageBox } from 'element-plus'
import {reactive, ref} from 'vue'
import axios from "axios";
import useClipboard from "vue-clipboard3";
const { toClipboard } = useClipboard();
export default {
  setup(){
    const copy=() =>{
      toClipboard(result.value[0])
      ElMessage({
        message:"复制成功",
        type:"success"
      })
    }
    const result= ref()
    const form = reactive({
      method:'',
      key1:'',
      key2:'',
      module:'false',
      cypherText:'',
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
        if(form.method!='DecryptText' && form.module){
          ElMessageBox.alert('CBC模式暂未开放', '提示', {
            autofocus: false,
            type:'success',
            confirmButtonText: '确认',
          })
        }else if(form.key1!=''){
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
            submitForm.value.key1=form.key1
            if(form.module){
              if(form.initVector!=''){
                submitForm.value.cypherTexts=form.cypherText.split(',')
                axios.post("http://localhost:8080/DecryptWordsCBC",submitForm.value).then((response)=>{
                  if(response.data.code==1){
                    dataVisible.value=false
                    result.value=[response.data.data.plainText]
                  }else{
                    ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }
                })
              }else{
                ElMessageBox.alert('请输入初始向量', '错误', {
                  autofocus: false,
                  type:'warning',
                  confirmButtonText: '确认',
                })
              }
            }else{
              if(form.method=='DecryptText'){
                submitForm.value.cypherTexts=form.cypherText.split(',')
                axios.post("http://localhost:8080/DecryptWords",submitForm.value).then((response)=>{
                  if(response.data.code==1){
                    dataVisible.value=false
                    result.value=[response.data.data.plainText]
                  }else{
                    ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                      autofocus: false,
                      type:'error',
                      confirmButtonText: '确认',
                    })
                  }
                })
              }else{
                if(form.cypherText.length!=16){
                  ElMessageBox.alert('请输入16位密文', '错误', {
                    autofocus: false,
                    type:'error',
                    confirmButtonText: '确认',
                  })
                }else if(!isBinaryString(form.cypherText)){
                  ElMessageBox.alert('密文只能包含0/1', '错误', {
                    autofocus: false,
                    type:'error',
                    confirmButtonText: '确认',
                  })
                }else {
                  submitForm.value.cypherText=form.cypherText
                  axios.post("http://localhost:8080/SAESDecrypt",submitForm.value).then((response)=>{
                    if(response.data.code==1){
                      dataVisible.value=false
                      result.value=[response.data.data.plainText]
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

        } else if((form.method=='DoubleDecrypt' || form.method=='TrippleDecrypt') && form.key1!='' && form.key2!=''){

        }else{
          ElMessageBox.alert('请补全密钥', '错误', {
            autofocus: false,
            type:'error',
            confirmButtonText: '确认',
          })
        }
      }else{
        ElMessageBox.alert('请输入解密方式', '错误', {
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
      copy,
      result
    }
    console.log(form.module)
  }
}
</script>

<template>
  <el-form :model="form" label-width="120px" :label-position="'top'" :size="'large'">
    <el-form-item label="解密方式">
      <el-select v-model="form.method" placeholder="选择你的解密方式">
        <el-option label="解密文字（长文本）" value="DecryptText" />
        <el-option label="解密密文（16位密文）" value="DecryptNormal" />

      </el-select>
    </el-form-item>
    <div v-if="form.method=='DecryptText'||form.method=='DecryptNormal'">
      <el-form-item label="密钥">
        <el-input v-model="form.key1" placeholder="请输入密钥"/>
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
    <div v-if="form.method=='DecryptText'">
      <el-form-item label="解密文字">
        <el-input v-model="form.cypherText" type="textarea" />
      </el-form-item>
    </div>
    <div v-else>
      <el-form-item label="解密密文">
        <el-input v-model="form.cypherText" placeholder="请输入需要解密的密文"/>
      </el-form-item>
    </div>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">解密</el-button>
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
        <div v-for="index in result" :key="index" class="text item">{{ index }}</div>
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