<template>
  <div class="labelsys__wrapper">
    <div class="labelsys__topper">
      <div class="labelsys__topper-img"></div>
      <div class="labelsys__topper-overlay"></div>
      <div class="labelsys__topper-title">
        <h1>运动员总览</h1>
      </div>
    </div>
    <el-card class="box-card">
      <!-- 搜索区域 -->
      <div class="search-box">
        <el-form :inline="true">
          <el-form-item label="标签分类">
            <el-select v-model="value" placeholder="请选择分类" size="small" @change="handleSelect">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="姓名">
            <el-select
              v-model="playerName"
              placeholder="请输入姓名"
              size="small"
              @change="handleSelectPlayerName"
              filterable
            >
              <el-option
                v-for="item in playerNameOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="性别">
            <el-select
              v-model="playerGender"
              placeholder="请选择性别"
              size="small"
              @change="handleSelectPlayerGender"
            >
              <el-option
                v-for="item in playerGenderOptions"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" size="small">筛选</el-button>
            <el-button type="info" size="small" @click="reset">重置</el-button>
          </el-form-item>
        </el-form>
      </div>

      <!-- 标签体系区域 -->
      <el-table
        ref="multipleTable"
        :data="changeinfo.slice((currentPage - 1) * pagesize, currentPage * pagesize)"
        height="600"
        tooltip-effect="dark"
        style="width: 90%, margin-left: 50px"
        border
        stripe
        @selection-change="handleSelectionChange"
      >
            <!-- <el-table-column fixed type="selection" width="55" align="center"></el-table-column> -->
            <el-table-column prop="name" label="姓名" align="center"></el-table-column>
            <el-table-column prop="birthday" label="出生日期" align="center"></el-table-column>
            <el-table-column prop="age" label="年龄" align="center"></el-table-column>
            <el-table-column prop="gender" label="性别" align="center"></el-table-column>
            <el-table-column prop="marital_status" label="婚姻状况" align="center"></el-table-column>
            <el-table-column prop="education_level" label="受教育程度" align="center"></el-table-column>
            <el-table-column prop="professional_training_years" label="专业训练年限（练赛艇算起）" width="120" align="center"></el-table-column>
            <el-table-column prop="training_experience" label="训练经历" align="center"></el-table-column>
            <el-table-column prop="sports_level" label="运动等级" align="center"></el-table-column>
            <el-table-column prop="height" label="身高" align="center"></el-table-column>
            <el-table-column prop="weight" label="体重" align="center"></el-table-column>
            <el-table-column fixed="right" label="操作" align="center" width="150">
                <template slot-scope="scope">
                  <div class="operatin-button">
                    <!-- <div class="addordelete-button">
                      <el-button type="primary" size="mini" @click="editBasicInfo(scope.row)">编辑</el-button>
                      <el-button type="danger" size="mini" @click="deleteBasicInfo(scope.$index, scope.row)">删除</el-button>
                    </div> -->
                    <div class="persona-button">
                      <el-button round size="mini" @click="goToPersona(scope.row)">画像</el-button>
                      <el-button round size="mini" @click="goToChampion(scope.row)">对比</el-button>
                    </div>
                  </div>
                </template>
            </el-table-column>
      </el-table>
      <el-pagination
        background
        :current-page="currentPage"
        :page-size="pagesize"
        :total="totalData"
        :page-sizes="[5, 10, 20, 30, 40, 50]"
        @current-change="handleCurrentChange"
        @size-change="handleSizeChange"
        layout="total, sizes, prev, pager, next, jumper" />
      <!-- 新增标签区域 -->
      <!-- <el-dialog title="新增标签" :visible.sync="dialogaddVisible">
        <el-form :model="basicInfo" ref="basicInfo">
            <el-form-item label="姓名" :label-width="formLabelWidth" prop="name">
              <el-input v-model="basicInfo.name" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="出生日期" :label-width="formLabelWidth" prop="birthday">
              <el-date-picker v-model="basicInfo.birthday" type="date" placeholder="选择日期" @change="getAge" format="yyyy-MM-dd" value-format="yyyy-MM-dd"></el-date-picker>
            </el-form-item>
            <el-form-item label="性别" :label-width="formLabelWidth" prop="gender">
              <el-radio-group v-model="basicInfo.gender" size="small">
                <el-radio label="男" border>男</el-radio>
                <el-radio label="女" border>女</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="婚姻状况" :label-width="formLabelWidth" prop="marital_status">
              <el-radio-group v-model="basicInfo.marital_status" size="small">
                <el-radio label="已婚" border>已婚</el-radio>
                <el-radio label="未婚" border>未婚</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="受教育程度" :label-width="formLabelWidth" prop="education_level">
              <el-select v-model="basicInfo.education_level" placeholder="请选择">
                <el-option
                  v-for="item in educationLevelOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="专业训练年限（练赛艇算起）" :label-width="formLabelWidth" prop="professional_training_years">
              <el-input v-model="basicInfo.professional_training_years" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="训练经历" :label-width="formLabelWidth" prop="training_experience">
              <el-input v-model="basicInfo.training_experience" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="运动等级" :label-width="formLabelWidth" prop="sports_level">
              <el-select v-model="basicInfo.sports_level" placeholder="请选择级别">
                <el-option label="轻量级" value="轻量级"></el-option>
                <el-option label="公开级" value="公开级"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="身高" :label-width="formLabelWidth" prop="height">
              <el-input v-model="basicInfo.height" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="体重" :label-width="formLabelWidth" prop="weight">
              <el-input v-model="basicInfo.weight" style="width: 180px"></el-input>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="ClearForm('basicInfo')">取 消</el-button>
            <el-button type="primary" @click="addBasicInfo">确 定</el-button>
        </div>
      </el-dialog> -->
      <!-- 修改标签区域 -->
      <!-- <el-dialog title="修改标签" :visible.sync="dialogupdateVisible">
        <el-form :model="basicInfo" ref="basicInfo">
            <el-form-item label="姓名" :label-width="formLabelWidth" prop="name">
              <el-input v-model="basicInfo.name" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="出生日期" :label-width="formLabelWidth" prop="birthday">
              <el-date-picker v-model="basicInfo.birthday" type="date" placeholder="选择日期" @change="getAge" format="yyyy-MM-dd" value-format="yyyy-MM-dd"></el-date-picker>
            </el-form-item>
            <el-form-item label="性别" :label-width="formLabelWidth" prop="gender">
              <el-radio-group v-model="basicInfo.gender" size="small">
                <el-radio label="男" border>男</el-radio>
                <el-radio label="女" border>女</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="婚姻状况" :label-width="formLabelWidth" prop="marital_status">
              <el-radio-group v-model="basicInfo.marital_status" size="small">
                <el-radio label="已婚" border>已婚</el-radio>
                <el-radio label="未婚" border>未婚</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="受教育程度" :label-width="formLabelWidth" prop="education_level">
              <el-select v-model="basicInfo.education_level" placeholder="请选择">
                <el-option
                  v-for="item in educationLevelOptions"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="专业训练年限（练赛艇算起）" :label-width="formLabelWidth" prop="professional_training_years">
              <el-input v-model="basicInfo.professional_training_years" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="训练经历" :label-width="formLabelWidth" prop="training_experience">
              <el-input v-model="basicInfo.training_experience" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="运动等级" :label-width="formLabelWidth" prop="sports_level">
              <el-select v-model="basicInfo.sports_level" placeholder="请选择级别">
                <el-option label="轻量级" value="轻量级"></el-option>
                <el-option label="公开级" value="公开级"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="身高" :label-width="formLabelWidth" prop="height">
              <el-input v-model="basicInfo.height" style="width: 180px"></el-input>
            </el-form-item>
            <el-form-item label="体重" :label-width="formLabelWidth" prop="weight">
              <el-input v-model="basicInfo.weight" style="width: 180px"></el-input>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="ClearForm('basicInfo')">取 消</el-button>
            <el-button type="primary" @click="updateBasicInfo">确 定</el-button>
        </div>
      </el-dialog> -->
    </el-card>
  </div>
</template>

<script>
import myAxios from '@/utils/request'
import { formatDate } from '@/utils/formatDate'
import { getAge } from '@/utils/getAge'
export default {
  data () {
    return {
      info: [],
      multipleSelection: [],
      dialogaddVisible: false,
      dialogupdateVisible: false,
      basicInfo: {
        name: '',
        birthday: '',
        age: '',
        gender: '',
        marital_status: '',
        education_level: '',
        professional_training_years: '',
        training_experience: '',
        sports_level: '',
        height: 0,
        weight: 0
      },
      totalData: 10,
      currentPage: 1, //  el-pagination 第几页
      pagesize: 10, //  el-pagination 每页的数据
      options: [{
        label: '重点培养',
        value: '重点培养'
      }],
      value: '',
      changeinfo: [],
      HuiXianid: '',
      formLabelWidth: '120px',
      playerName: '',
      playerNameOptions: [],
      playerGender: '',
      playerGenderOptions: [{
        label: '男',
        value: '男'
      },
      {
        label: '女',
        value: '女'
      }],
      educationLevelOptions: [{
        label: '初中',
        value: '初中'
      },
      {
        label: '高中',
        value: '高中'
      },
      {
        label: '中专',
        value: '中专'
      },
      {
        label: '大专',
        value: '大专'
      }]
    }
  },
  mounted () {
    this.getPlayerName()
    this.getBasicInfo()
  },
  methods: {
    handleSelectPlayerGender (gender) {
      this.changeinfo = this.info.filter(item => {
        return item.gender === gender
      })
      this.totalData = this.changeinfo.length
    },
    getPlayerName () {
      axios.get('http://localhost/list/searchName').then(res => {
        const temp = res.data.data.map(item => {
          return {
            label: item.name,
            value: item.name
          }
        })
        this.playerNameOptions = temp
        // console.log(this.playerNameOptions)
      }).catch(err => {
        console.log('获取数据失败' + err)
      })
    },
    handleSelectPlayerName (name) {
      this.changeinfo = this.info.filter(item => {
        return item.name === name
      })
      this.totalData = this.changeinfo.length
    },
    reset () {
      this.value = ''
      this.playerName = ''
      this.playerGender = ''
      this.changeinfo = this.info
      this.totalData = this.changeinfo.length
    },
    goToPersona (row) {
      this.$router.push({ name: 'playergroup-one', params: { id: row.id } })
    },
    goToChampion (row) {
      this.$router.push({ name: 'champion-model', params: { id: row.id } })
    },
    handleSelect (value) {
      if (value === '重点培养') {
        this.changeinfo = this.info.filter(item => {
          return parseFloat(item.VO2MAX) > 4
        })
        this.changeinfo.sort((a, b) => b.VO2MAX - a.VO2MAX)
        this.totalData = this.changeinfo.length
      }
    },
    handleSizeChange (size) {
      this.pagesize = size
      // 每次改变页码大小,重定向第一页
      // this.currentPage = 1
    },
    // 点击第几页把页码传进来，发送接口请求改变数据
    handleCurrentChange (currentPage) {
      this.currentPage = currentPage
    },
    // 清空表单数据
    ClearForm (formName) {
      this.dialogaddVisible = false
      this.dialogupdateVisible = false
      this.$refs[formName].resetFields()// 点击取消按钮，清空el-input
    },
    getBasicInfo () { // 查找info表全部数据
      axios.get('http://localhost/list/getBasicInfo').then(res => {
        // console.log(res.data)
        const tmp = res.data
        const formatTmp = tmp.map(item => (
          {
            ...item,
            birthday: formatDate(item.birthday)
          }))
        this.info = formatTmp
        this.changeinfo = formatTmp
        this.totalData = res.data.length
      }).catch(err => {
        console.log('获取数据失败' + err)
      })
    },
    toggleSelection (rows) {
      if (rows) {
        rows.forEach((row) => {
          this.$refs.multipleTable.toggleRowSelection(row)
        })
      } else {
        this.$refs.multipleTable.clearSelection()
      }
    },
    handleSelectionChange (val) {
      this.multipleSelection = val
    },
    // rowClass ({ rowIndex, columnIndex }) {
    //   if (rowIndex === 0) {
    //     if (columnIndex === 9 || columnIndex === 13) {
    //       return {color: 'red'}
    //     }
    //   }
    // },
    // cellStyle ({row, column, rowIndex, columnIndex}) {
    //   if (column.property === 'VO2MAX' || column.property === 'CGY') {
    //     return {color: 'red', 'font-weight': 700}
    //   }
    // },
    getAge (e) {
      this.basicInfo.age = getAge(e)
    },
    deleteBasicInfo (index, row) { // 删除操作
      // console.log(index, row)
      axios.get('http://localhost/list/deleteBasicInfo', {
        params: {
          id: row.id
        }
      }).then(res => {
        if (res.data.status === 200) {
          this.$message({
            message: '删除成功'
          })
          this.getBasicInfo()
        } else {
          this.$message({
            message: '删除失败',
            type: 'error'
          })
        }
      }).catch(err => {
        console.log('操作失败' + err)
      })
    },
    addBasicInfo () { // 添加操作
      // console.log(this.basicInfo.age)
      axios.get('http://localhost/list/addBasicInfo', {
        params: {
          name: this.basicInfo.name,
          birthday: this.basicInfo.birthday,
          age: this.basicInfo.age,
          gender: this.basicInfo.gender,
          marital_status: this.basicInfo.marital_status,
          education_level: this.basicInfo.education_level,
          professional_training_years: this.basicInfo.professional_training_years,
          training_experience: this.basicInfo.training_experience,
          sports_level: this.basicInfo.sports_level,
          height: this.basicInfo.height,
          weight: this.basicInfo.weight
        }
      }).then(res => {
        if (res.data.status === 200) {
          this.$message({
            message: '添加成功'
          })
          this.dialogaddVisible = false
          this.getBasicInfo()
          this.ClearForm(this.basicInfo)
        } else {
          this.$message({
            message: '添加失败',
            type: 'error'
          })
        }
      }).catch(err => {
        console.log('操作失败' + err)
      })
    },
    editBasicInfo (row) { // 回显数据
      this.dialogupdateVisible = true
      this.HuiXianid = row.id
      this.$nextTick(() => {
        // 先让对话框出现, 它绑定空值的对象, 才能resetFields清空用初始空值
        this.basicInfo.name = row.name
        this.basicInfo.birthday = row.birthday
        this.basicInfo.age = row.age
        this.basicInfo.gender = row.gender
        this.basicInfo.marital_status = row.marital_status
        this.basicInfo.education_level = row.education_level
        this.basicInfo.professional_training_years = row.professional_training_years
        this.basicInfo.training_experience = row.training_experience
        this.basicInfo.sports_level = row.sports_level
        this.basicInfo.height = row.height
        this.basicInfo.weight = row.weight
      })
    },
    updateBasicInfo () { // 修改操作
      axios.get('http://localhost/list/updateBasicInfo', {
        params: {
          id: this.HuiXianid,
          name: this.basicInfo.name,
          birthday: this.basicInfo.birthday,
          age: this.basicInfo.age,
          gender: this.basicInfo.gender,
          marital_status: this.basicInfo.marital_status,
          education_level: this.basicInfo.education_level,
          professional_training_years: this.basicInfo.professional_training_years,
          training_experience: this.basicInfo.training_experience,
          sports_level: this.basicInfo.sports_level,
          height: this.basicInfo.height,
          weight: this.basicInfo.weight
        }
      }).then(res => {
        if (res.data.status === 200) {
          this.$message({
            message: '修改成功'
          })
          this.dialogupdateVisible = false
          this.getBasicInfo()
        } else {
          this.$message({
            message: '修改失败',
            type: 'error'
          })
        }
      }).catch(err => {
        console.log('操作失败' + err)
      })
    }
  }
}
</script>

<style lang='less' scoped>
.labelsys {
  &__wrapper {
    width: 100%;
  }
  &__topper {
    height: 600px;
    width: 100%;
    position: relative;
    &-img {
      height: 100%;
      background-image: url('../../assets/images/444.jpg');
      background-position: center;
      background-size: cover;
      background-repeat: no-repeat;
    }
    &-overlay {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: transparent linear-gradient(180deg,rgba(0,57,124,.1),rgba(0,44,94,.3) 22%,rgba(0,37,79,.5) 50%,#001d3e) 0 0 no-repeat padding-box;
    }
    &-title {
      width: 100%;
      position: absolute;
      bottom: 60px;
      left: 0;
      right: 0;
      h1 {
        width: 80%;
        margin: 0 auto;
        font-family: "Effra",Arial,sans-serif;
        font-style: italic;
        font-size: 50px;
        color: white;
        line-height: 75px;
      }
    }
  }
}

.box-card {
  margin: 0 auto;
  width: 95%;
}

.operatin-button {
  width: 100%;
  // display: flex;
  // flex-direction: column;
  // align-items: center;
}
// .addordelete-button {
//   display: flex;
//   flex-direction: row;
//   justify-content: flex-start;
//   margin-bottom: 5px;
// }
.persona-button {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
</style>
