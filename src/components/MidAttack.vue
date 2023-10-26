<script>
import {reactive, ref} from "vue";
import {ElMessage, ElMessageBox} from "element-plus";
import axios from "axios";
import useClipboard from "vue-clipboard3";
const { toClipboard } = useClipboard();
export default {
  setup(){
    const copykey1=() =>{
      toClipboard(result.value[0])
      ElMessage({
        message:"复制成功",
        type:"success"
      })
    }
    const copykey2=() =>{
      toClipboard(result.value[0])
      ElMessage({
        message:"复制成功",
        type:"success"
      })
    }
    const resKey1=ref(null)
    const resKey2=ref(null)
    const tableVisible=ref(true)
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
    const dataVisible=ref(true)
    const processVisible=ref(false)
    const success=ref(false)
    const pairs=ref([
    ])
    const newPairs=ref([

    ])
    const attack=()=>{
      processVisible.value=true
      if(newPairs.value.length<3){
        ElMessageBox.alert('明密文对少，可能无法破解', 'Warn', {
          autofocus: false,
          type:'error',
          confirmButtonText: '确认',
        }).then(()=>{
          ElMessage({
            message: '尝试继续破解.',
            type: 'warning',
          })
          for (let index = 0; index < newPairs.value.length; index++) {
            const pair = newPairs.value[index];
            submitForm.value.plainTexts.push(pair.plainText);
            submitForm.value.cypherTexts.push(pair.cypherText);
          }
          axios.post("http://localhost:8080/Attack",submitForm.value).then((response)=>{
            if(response.data.code==1){
              pairs.value=response.data.data.possibleKeys
              processVisible.value=false
              tableVisible.value=false
              success.value=true
              console.log(pairs.value)
            }else{
              ElMessageBox.alert('内部错误，请联系管理员', '错误', {
                autofocus: false,
                type:'error',
                confirmButtonText: '确认',
              })
            }
          })
        })
      }else{
        ElMessage({
          message: '开始破解',
          type: 'success',
        })
        for (let index = 0; index < newPairs.value.length; index++) {
          const pair = newPairs.value[index];
          submitForm.value.plainTexts.push(pair.plainText);
          submitForm.value.cypherTexts.push(pair.cypherText);
        }
        processVisible.value=true
        axios.post("http://localhost:8080/Attack",submitForm.value).then((response)=>{
          if(response.data.code==1){
            resKey1.value=response.data.data.possibleKeys[0][0]
            resKey2.value=response.data.data.possibleKeys[0][1]
            processVisible.value=false
            tableVisible.value=false
            success.value=false
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
    const addNew = () => {
      ElMessageBox.prompt('添加新的明密文对', 'Tip', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Cancel',
        inputPlaceholder: '16BitPlainText:16BitCypherText'
      }).then((value) => {
            const plainText = value.value.substring(0, 16);
            const cypherText = value.value.substring(17, 33);
            const pair = {
              plainText,
              cypherText
            };
            // Assuming 'pairs' is a ref or reactive variable
            newPairs.value.push(pair);
            dataVisible.value=false
            ElMessage({
              type: 'success',
              message: `Added new pair: PlainText=${plainText}, CypherText=${cypherText}`,
            });
          })
          .catch(() => {
            ElMessage({
              type: 'info',
              message: 'Input canceled',
            });
          });
    }
    return{
      dataVisible,
      pairs,
      addNew,
      attack,
      processVisible,
      newPairs,
      success,
      resKey1,
      resKey2,
      tableVisible,
      copykey1,
      copykey2
    }
  }
}
</script>

<template>
  <div v-if="dataVisible">
    <el-empty description="没有数据" />
  </div>
  <div v-else>
    <el-table :data="newPairs" height="200" style="width: 100%">
      <el-table-column prop="plainText" label="明文" width="700" />
      <el-table-column prop="cypherText" label="密文" width="800"/>
    </el-table>
  </div>
  <el-button type="primary" @click="addNew">增加明密文对</el-button>
  <div v-if="tableVisible" v-loading="processVisible">
    <el-empty description="没有数据" />
  </div>
  <div v-else style="margin-top: 20px">
    <div v-if="success">
      <el-card class="box-card" style="width: 100% ">
        <template #header>
          <div class="card-header">
            <span><el-icon><Document /></el-icon></span>
          </div>
        </template>
        <div v-for="res in pairs" :key="o" class="text item">{{ "密钥一: "+res[0] +" ; "+"密钥二: "+ res[1] }}</div>
      </el-card>
    </div>
    <div v-else>
      <span>Success ！</span>
      <el-divider border-style="dashed" content-position="right"><el-text class="mx-1" type="success">成功破解！</el-text></el-divider>
      <el-row :gutter="12">
        <el-col :span="12">
          <el-card shadow="hover" @click="copykey1"> {{resKey1}} </el-card>
        </el-col>
        <el-col :span="12">
          <el-card shadow="hover" @click="copykey2">{{resKey2}}</el-card>
        </el-col>
      </el-row>
    </div>
  </div>
  <el-button type="primary" @click="attack" style="margin-top: 20px">开始破解</el-button>
</template>

<style scoped>

</style>