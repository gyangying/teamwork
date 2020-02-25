<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-row :gutter="20">
          <el-col :span="6">学科：
            <el-select v-model="searchForm.subjectID" class="wd">
              <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">难度：
            <el-select v-model="searchForm.difficulty" class="wd">
              <el-option
              v-for="item in difficultyList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">试题类型：
            <el-select v-model="searchForm.questionType" class="wd">
              <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">标签：
            <!-- <el-select></el-select> -->
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
import {
  difficulty as difficultyList,
  questionType as questionTypeList
  } from '@/api/hmmm/constants.js'
import {simple} from '@/api/hmmm/subjects.js'
export default {
  name: 'QuestionsList',
  data() {
    return {
      difficultyList,
      questionTypeList,
      subjectIDList: [],
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: ''
      }
    }
  },
  created () {
    this.getSubjectIDList()
  },
  methods: {
    async getSubjectIDList () {
      const res = await simple()
      this.subjectIDList = res.data 
    }
  }
}
</script>

<style scoped>
.wd {
  width: 170px;
}
</style>
