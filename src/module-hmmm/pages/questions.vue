<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-row :gutter="20">
          <el-col :span="6">学&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;科：
            <el-select v-model="searchForm.subjectID" class="wd">
              <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">难&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;度：
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
          <el-col :span="6">标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签：
            <el-select v-model="searchForm.tags" class="wd">
              <el-option
              v-for="item in tagsList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">城&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;市：
            <el-select
            v-model="searchForm.province"
            class="wd1"
            placeholder="选城市"
            @change="searchForm.city=''">
              <el-option
              v-for="(item, k) in provinces()"
              :key="k"
              :label="item"
              :value="item"
              ></el-option>
            </el-select>
            <el-select
            v-model="searchForm.city"
            palceholder="选区县"
            class="wd1">
              <el-option
              v-for="(item, k) in citys(searchForm.province)"
              :key="k"
              :label="item"
              :value="item"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">关&nbsp;&nbsp;键&nbsp;&nbsp;字：
            <el-input type="text" class="wd"></el-input>
          </el-col>
          <el-col :span="6">题目备注：
            <el-input type="text" class="wd"></el-input>
          </el-col>
          <el-col :span="6">企业简称：
            <el-input type="text" class="wd"></el-input>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="6">方&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;向：
            <el-select v-model="searchForm.direction" class="wd">
              <el-option
              v-for="(item, k) in directionList"
              :key="k"
              :label="item"
              :value="k"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">录&nbsp;&nbsp;入&nbsp;&nbsp;人：
            <el-select v-model="searchForm.creatorID" class="wd">
              <el-option
              v-for="item in creatorIDList"
              :key="item.id"
              :value="item.id"
              :label="item.username"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">二级目录：
            <el-select v-model="searchForm.catalogID" class="wd">
              <el-option
              v-for="item in catalogIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            <el-button size="mini">清除</el-button>
            <el-button type="primary" size="mini">搜索</el-button>
          </el-col>
        </el-row>
        <el-table :data="questionList" style="100%">
          <el-table-column label="序号" type="index"></el-table-column>
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column
           :formatter="questionTypeFMT"
           label="题型"
           prop="questionType"
          ></el-table-column>
          <el-table-column label="题干" prop="question"></el-table-column>
          <el-table-column label="录入时间" width="170">
            <template slot-scope="stData">
              {{stData.row.addDate | parseTimeByString}}
            </template>
          </el-table-column>
          <el-table-column
           :formatter="difficultyFMT"
           label="难度" 
           prop="difficulty"
           ></el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <el-table-column label="操作" width="200">
            <a href="#">预览</a>
            <a href="#">修改</a>
            <a href="#">删除</a>
            <a href="#">加入精选</a>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
  </div>
</template>

<script>
import {list} from '@/api/hmmm/questions.js'
import {provinces, citys} from '@/api/hmmm/citys.js'
import {simple as simpleDirectorys} from '@/api/hmmm/directorys.js'
import {simple as simpleUsers} from '@/api/base/users.js'
import {simple as simpleTags} from '@/api/hmmm/tags.js'
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
  } from '@/api/hmmm/constants.js'
import {simple} from '@/api/hmmm/subjects.js'
export default {
  name: 'QuestionsList',
  data() {
    return {
      questionList: [],
      directionList,
      catalogIDList: [],
      creatorIDList: [],
      tagsList: [],
      difficultyList,
      questionTypeList,
      subjectIDList: [],
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: '',
        tags: '',
        province: '',
        city: '',
        keyword: '',
        remarks: '',
        shortName: '',
        direction: '',
        creatorID: '',
        catalogID: ''
      }
    }
  },
  created () {
    this.getSubjectIDList()
    this.getTagsList()
    this.getCreatorIDList()
    this.getCatalogIDList()
    this.getQuestionList()
  },
  watch: {
    searchForm: {
      handler: function() {
        this.getQuestionList()
      },
      deep: true
    }
  },
  methods: {
    difficultyFMT (row, column, cellValue, index) {
      return this.difficultyList[cellValue - 1].label
    },
    questionTypeFMT (row, column, cellValue, index) {
      return this.questionTypeList[cellValue - 1].label
    },
    async getQuestionList () {
      const res = await list(this.searchForm)
      console.log(res)
      this.questionList = res.data.items
    },
    provinces,
    citys,
    async getCatalogIDList() {
      const res = await simpleDirectorys()
      // console.log(res)
      this.catalogIDList = res.data
    },
    async getCreatorIDList() {
      const res = await simpleUsers()
      // console.log(res)
      this.creatorIDList = res.data
    },
    async getTagsList() {
      const res = await simpleTags()
      // console.log(res)
      this.tagsList = res.data
    },
    async getSubjectIDList () {
      const res = await simple()
      this.subjectIDList = res.data 
    }
  }
}
</script>

<style scoped>
.wd {
  width: 160px;
}
.wd1 {
  width: 85px;
}
.el-row {
  margin-bottom: 10px;
}
.el-table {
  margin-top: 25px;
}
</style>
