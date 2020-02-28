<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-form ref="addFormRef" :model="addForm" label-width="120px">
          <el-form-item label="学科：">
            <el-select v-model="addForm.subjectID">
              <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录：">
            <el-select v-model="addForm.catalogID">
              <el-option
              v-for="item in catalogIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业：">
            <el-select v-model="addForm.enterpriseID">
              <el-option
              v-for="item in enterpriseIDList"
              :key="item.id"
              :label="item.shortName"
              :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市：">
            <el-select v-model="addForm.province" @change="getCitys(addForm.province)">
              <el-option
              v-for="(item, k) in provinces()"
              :key="k"
              :label="item"
              :value="item"
              ></el-option>
            </el-select>
            <el-select v-model="addForm.city">
              <el-option
              v-for="(item, k) in citys(addForm.province)"
              :key="k"
              :label="item"
              :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向：">
            <el-select v-model="addForm.direction">
              <el-option
              v-for="item in directionList"
              :key="item"
              :label="item"
              :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="题型：">
            <el-radio-group v-model="addForm.questionType">
              <el-radio
              v-for="item in questionTypeList"
              :key="item.value"
              :label="item.value+''"
              >{{item.label}}
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="难度：">
            <el-radio-group v-model="addForm.difficulty">
              <el-radio
              v-for="item in difficultyList"
              :key="item.value"
              :label="item.value+''"
              >{{item.label}}
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="题干：">
            <el-input type="textarea" v-model="addForm.question"></el-input>
          </el-form-item>
          <el-form-item label="选项："  v-if="addForm.questionType !== '3'">
            <template v-if="addForm.questionType === '1'">
              <el-radio v-model="singleSelect" :label="0">
                A: <el-input type="text" v-model="addForm.options[0].title"></el-input>
              </el-radio> <br/> 
              <el-radio v-model="singleSelect" :label="1">
                B: <el-input v-model="addForm.options[1].title"></el-input>
              </el-radio> <br/>
              <el-radio v-model="singleSelect" :label="2">
                C: <el-input v-model="addForm.options[2].title"></el-input>
              </el-radio> <br/>
              <el-radio v-model="singleSelect" :label="3">
                D: <el-input v-model="addForm.options[3].title"></el-input>
              </el-radio> <br/>
            </template>
            <template v-if="addForm.questionType === '2'">
              <el-checkbox v-model="addForm.options[0].isRight">
                A: <el-input v-model="addForm.options[0].title"></el-input>
              </el-checkbox> <br/>
              <el-checkbox v-model="addForm.options[1].isRight">
                B: <el-input v-model="addForm.options[1].title"></el-input>
              </el-checkbox> <br/>
              <el-checkbox v-model="addForm.options[2].isRight">
                C: <el-input v-model="addForm.options[2].title"></el-input>
              </el-checkbox> <br/>
              <el-checkbox v-model="addForm.options[3].isRight">
                D: <el-input v-model="addForm.options[3].title"></el-input>
              </el-checkbox> <br/>
            </template>
          </el-form-item>
          <el-form-item label="答案：" v-if="addForm.questionType === '3'">
            <el-input type="textarea" v-model="addForm.answer"></el-input>
          </el-form-item>
          <el-form-item label="备注：">
            <el-input type="textarea" v-model="addForm.remarks"></el-input>
          </el-form-item>
          <el-form-item label="标签：">
            <el-input type="text" v-model="addForm.tags"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button @click="tianjia()" type="primary">提交</el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import {add} from '@/api/hmmm/questions.js'
import {list as companysList} from '@/api/hmmm/companys.js'
import {simple as subjectsSimple} from '@/api/hmmm/subjects.js'
import {provinces, citys} from '@/api/hmmm/citys'
import {simple as directorysSimple} from '@/api/hmmm/directorys'
import {
  direction as directionList,
  questionType as questionTypeList,
  difficulty as difficultyList
} from '@/api/hmmm/constants.js'
export default {
  name: 'QuestionsNew',
  data() {
    return {
      difficultyList,
      questionTypeList,
      enterpriseIDList: [],
      subjectIDList: [],
      catalogIDList: [],
      directionList,
      singleSelect: '',
      addForm: {
        options: [
          { code: 'A', title: '', img: '', isRight: false },
          { code: 'B', title: '', img: '', isRight: false },
          { code: 'C', title: '', img: '', isRight: false },
          { code: 'D', title: '', img: '', isRight: false }
        ],
        difficulty: '1',
        questionType: '1',
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        province: '',
        city: '',
        direction: '',
        question: '',
        answer: '',
        remarks: '',
        tags: '',
        videoURL: 'http://www.xxx.com'
      }
    }
  },
  created() {
    this.getsubjectIDList()
    this.getCatalogIDList()
    this.getEnterpriseIDList()
  },
  watch: {
    singleSelect (newVal, oldVal) {
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newVal].isRight = true
    }
  },
  methods: {
    provinces,
    citys,
    async tianjia() {
      const res = await add(this.addForm)
      this.$message.success('添加成功')
      this.$router.push('/questions/list')
    },
    async getEnterpriseIDList () {
      const res = await companysList()
      this.enterpriseIDList = res.data.items
    },
    async getsubjectIDList () {
      const res = await subjectsSimple()
      this.subjectIDList = res.data
    },
    async getCatalogIDList () {
      const res = await directorysSimple()
      this.catalogIDList = res.data
    }
  }
}
</script>

<style scoped>
</style>
