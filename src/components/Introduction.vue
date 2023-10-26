<template>
  <div class="demo-collapse">
    <el-collapse v-model="activeName" accordion>
      <el-collapse-item title="Simplified AES" name="1">
        <div>
          SAES（Simplified AES）: S-AES之于AES就像S-DES之于DES。事实上，S-AES的结构与AES完全相同。区别在于密钥大小（16 位）、块大小（16 位和轮数（2 轮）。
        </div>
        <div>
          Consistent within interface: all elements should be consistent, such
          as: design style, icons and texts, position of elements, etc.
        </div>
      </el-collapse-item>
      <el-collapse-item title="CBC模式" name="2">
        <div>
          CBC（Cipher-block chaining）: 1976年，IBM发明了密码分组链接（CBC，Cipher-block chaining）模式[14]。在CBC模式中，每个明文块先与前一个密文块进行异或后，再进行加密;
        </div>
        <div>
          在这种方法中，每个密文块都依赖于它前面的所有明文块。同时，为了保证每条消息的唯一性，在第一个块中需要使用初始化向量。
        </div>
      </el-collapse-item>
      <el-collapse-item title="中间相遇攻击" name="3">
        <div>
          中途相遇攻击（Meet-in-the-middle attack）: 是密码学上以空间换时间的一种攻击。
        </div>
        <div>
          这个攻击方式在1977年就由惠特菲尔德·迪菲（Diffie）与马丁·赫尔曼（Hellman）提出来。
        </div>
        <div>
          当攻击者已知明文P与密文C时，攻击者可以穷举所有k1的组合，将产生出来的第一层密文ENCk1(P),用大量空间储存下来。再穷举所有k2的组合，将
        </div>
        <div>
          DECk2(C)的值与前面储存下来的结果比对，进而得出正确的k1和k2。
        </div>
      </el-collapse-item>
      <el-collapse-item title="关于我们" name="4">
        <div>
          我们是重庆大学大数据与软件学院2021级软件工程专业学生；
        </div>
        <div>
          我们致力于将密码学中的经典加密算法通过可视化的方式呈现出来。
        </div>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const activeName = ref('1')
</script>
