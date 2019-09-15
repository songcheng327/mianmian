<template>
  <div class="dashboard-container">
    <div class="app-container">
         <el-form>
            <el-row :gutter=10>
              <el-col :span=6>学科:
                <el-select v-model="searchForm.subjectID">
                  <el-option  v-for="item in subjectList" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-col> 
              
              <el-col :span=6>难度:
                <el-select v-model="searchForm.difficultyID">
                  <el-option  v-for="item in difficultyList " :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-col> 

              <el-col :span=6>试题类型:
                <el-select style="width:150px" v-model="searchForm.questionTypeID">
                    <el-option v-for="item in questionTypeList" :key="item" :label="item.label" :value="item.value"></el-option>
                </el-select>
              </el-col>

              <el-col :span=6>城市:
                <el-select style="width:100px" @change="getcityList" v-model="searchForm.province">
                  <el-option  v-for="item in provinces " :key="item" :label="item" :value="item"></el-option>
                </el-select>
                <el-select style="width:100px" v-model="searchForm.city">
                  <el-option  v-for="item in cityList " :key="item" :label="item" :value="item"></el-option>
                </el-select>
              </el-col>
            </el-row>

            <el-row>
              <el-col :span=6>关键字:
                <el-input v-model="searchForm.keyword" style="width:200px" placeholder="请输入题目编号/题干"></el-input>
              </el-col>
              <el-col :span=6>题目备注:
                <el-input v-model="searchForm.remarks" style="width:150px" placeholder="请输入内容"></el-input>
              </el-col>
              <el-col :span=6>企业简称:
                <el-input v-model="searchForm.shortName" style="width:150px" placeholder="请输入内容"></el-input>
              </el-col>
              <el-col :span=6>方向:
                <el-select style="width:150px" v-model="searchForm.directionID">
                    <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
                </el-select>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span=6>录入人:
                <el-select style="width:150px" v-model="searchForm.creatorID">
                    <el-option v-for="item in creatorIDList" :key="item.id" :label="item.username" :value="item.id"></el-option>
                </el-select>
              </el-col>
              <el-col :span=6>二级目录:
                <el-select style="width:150px" v-model="searchForm.catalogID">
                    <el-option v-for="item in catalogIDList" :key="item.value" :label="item.label" :value="item.value"></el-option>
                </el-select>
            
              </el-col>
              <el-col :span=12 class="searchclean">
                <el-button>清除</el-button>
                <el-button type="primary">搜索</el-button>
              </el-col>
            </el-row>
            <el-row>
              <el-table :data="questionList" border style="width: 100%">
                <el-table-column  type="index" label="序号" width="80"></el-table-column>
                <el-table-column  prop="number" label="试题编号" width="180"> </el-table-column>
                <el-table-column  prop="subjectID"  label="学科"> </el-table-column>
                <el-table-column  prop="questionType"  label="题型" :formatter="questionTypeFMA"> </el-table-column>
                <el-table-column  prop="question"  label="题干" > </el-table-column>
                <el-table-column  prop="addDate"  label="录入时间">
                  <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
                </el-table-column>
                <el-table-column  prop="difficulty"  label="难度" :formatter="difficultyTypeFMA"> </el-table-column>
                <el-table-column  prop="creator"  label="录入人 "> </el-table-column>
                <el-table-column  label="操作">
                <a href="#">预览</a>
                <a href="#">修改</a>
                <a href="#">删除</a>
                <a href="#">加入精选</a>
                </el-table-column>
              </el-table>
            </el-row>
         </el-form> 
    </div>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects'
import {difficulty, questionType} from '@/api/hmmm/constants'
import {provinces, citys} from '../../api/hmmm/citys'
import {direction} from '../../api/hmmm/constants'
import {simple as creatorID} from '../../api/base/users'
import {simple as catalogID} from '../../api/hmmm/directorys'
import {list} from '../../api/hmmm/questions'
export default {
  name: 'QuestionsList',

  data() {
    return {
      subjectList: [],
      difficultyList: difficulty,
      provinces: provinces(),
      cityList: [],
      questionTypeList: questionType,
      directionList: direction,
      creatorIDList: [],
      catalogIDList: [],
      questionList: [],

      searchForm: {
        subjectID: '',
        difficultyID: '',
        province: '',
        city: '',
        questionTypeID: '',
        directionID: '',
        keyword: '',
        remarks: '',
        shortName: '',
        creatorID: '',
        catalogID: '',
        questionListID: ''

      }
    }
  },
  methods: {
    async getsubjectList() {
      var res = await simple() 
      this.subjectList = res.data
    },
    getcityList(pname) {
      this.searchForm.city = ''
      this.cityList = citys(pname)
    },
    async getcreatorIDList() {
      var res = await creatorID() 
      this.creatorIDList = res.data
    },
    async getcatalogID() {
      var res = await catalogID() 
      this.catalogIDList = res.data
    },
    async getquestionList() {
      var res = await list()
      console.log(res)
      
      this.questionList = res.data.items
    },
    questionTypeFMA(row, column, cellvalue) {
      return this.questionTypeList[cellvalue - 1][`label`]
    },
    difficultyTypeFMA(row, column, cellvalue) {
      return this.difficultyList[cellvalue - 1][`label`]
    }
  },
  created() {
    this.getsubjectList()
    // this.getdifficultyList()
    provinces()
    this.getcreatorIDList()
    this.getcatalogID()
    this.getquestionList() 
  }
}
</script>
<style scoped> 
  .el-row{
    margin-top:15px;
  }
</style>
