<template>
  <div class="summary">
    <div class="management">
      <img src="../../assets/management.png" width="100%" height="100px" alt="">
    </div>
    <div class="realize">
    <!-- 一键切换 -->
      <el-switch
        v-model="switchValue"
        @change="changeSwitch"
        active-text="评价"
        inactive-text="分数"
        :disabled="disability">
      </el-switch>
        <!-- 查询框 -->
      <el-input
        placeholder="请输入内容"
        v-model="search.value"
        class="input-with-select"
        @keyup.enter.native="searchData"
        clearable>
        <el-select
          v-model="search.select"
          slot="prepend">
          <el-option
            v-for="item in search.option"
            :key="item.id"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <el-button slot="append" icon="el-icon-search" @click="searchData"></el-button>
      </el-input>
      <el-popover
        placement="top"
        v-model="logoutVisible">
        <p>确定退出？</p>
        <div style="text-align: right; margin: 0">
          <el-button size="mini" type="text" @click="logoutVisible = false">取消</el-button>
          <el-button type="primary" size="mini" @click="logout">确定</el-button>
        </div>
        <el-button class="logout" type="danger" size="small" slot="reference">退出</el-button>
      </el-popover>
      <el-button class="refresh" type="primary" size="small" @click="refresh" :loading="refreshLoading">刷新</el-button>
    </div>
    <el-table
      :data="tableData.slice((staffCurrentPage-1)*staffPageSize,staffCurrentPage*staffPageSize)"
      v-loading="tableLoading"
      border
      style="height: 100%;margin-left: auto; margin-right: auto;text-align: center;"
      height="525"
      :default-sort = "{prop: 'date', order: 'descending'}"
      ref="mainTable">
      <el-table-column
        label="序号"
        prop="Number"
        header-align=center
        sortable>
      </el-table-column>
      <el-table-column
        prop="staffName"
        label="姓名"
        header-align=center>
      </el-table-column>
      <el-table-column
        prop="scorePL"
        label="智多星(PL)"
        header-align=center>
        <template slot-scope="scope">
         <el-tag class="evalTag" :type="getType(scope.row.PL)" v-show="evalTagShow">
            {{getEval(scope.row.PL)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.PL)" v-show="scoreTagShow">
            {{scope.row.scorePL}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="外联者(RI)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.RI)" v-show="evalTagShow">
            {{getEval(scope.row.RI)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.RI)" v-show="scoreTagShow">
            {{scope.row.scoreRI}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="协调者(CO)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.CO)" v-show="evalTagShow">
            {{getEval(scope.row.CO)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.CO)" v-show="scoreTagShow">
            {{scope.row.scoreCO}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="鞭策者(SH)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.SH)" v-show="evalTagShow">
            {{getEval(scope.row.SH)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.SH)" v-show="scoreTagShow">
            {{scope.row.scoreSH}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="监督者(ME)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.ME)" v-show="evalTagShow">
            {{getEval(scope.row.ME)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.ME)" v-show="scoreTagShow">
            {{scope.row.scoreME}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="凝聚者(TW)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.TW)" v-show="evalTagShow">
            {{getEval(scope.row.TW)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.TW)" v-show="scoreTagShow">
            {{scope.row.scoreTW}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="实干者(IM)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.IM)" v-show="evalTagShow">
            {{getEval(scope.row.IM)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.IM)" v-show="scoreTagShow">
            {{scope.row.scoreIM}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="善始善终者(CF)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.CF)" v-show="evalTagShow">
            {{getEval(scope.row.CF)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.CF)" v-show="scoreTagShow">
            {{scope.row.scoreCF}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="专家(SP)"
        header-align=center>
        <template slot-scope="scope">
          <el-tag class="evalTag" :type="getType(scope.row.SP)" v-show="evalTagShow">
            {{getEval(scope.row.SP)}}
          </el-tag>
          <el-tag class="scoreTag" :type="getType(scope.row.SP)" v-show="scoreTagShow">
            {{scope.row.scoreSP}}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="答题时间"
        prop="createTime"
        header-align=center
        sortable>
        <template slot-scope="scope">
          <span class="nowrap">{{ $utils.goodTime(scope.row.createTime) }}</span>
        </template>
      </el-table-column>
      <el-table-column
        label="详情"
        header-align=center>
        <template slot-scope="scope">
          <el-button @click="getDetails(scope.row.Number)" width="40px">详情</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="详情"
      :visible.sync="dialogVisible"
      width="28%">
      <el-form :label-position="labelPosition" label-width="80px">
        <el-form-item label="员工编号">
          {{staffData.staffNo}}
        </el-form-item>
        <el-form-item label="手机号">
          {{staffData.mobile}}
        </el-form-item>
        <el-form-item label="总评">
          <el-tag v-for="tag in staffData.tags" v-bind:key="tag" style="width: 75px;text-align: center;">
          {{tag}}
          </el-tag>
        </el-form-item>
        <el-form-item label="问卷名">
          《{{staffData.surveyName}}》
        </el-form-item>
        <el-form-item label="测试能力">
          {{staffData.surveyShow}}
        </el-form-item>
        <el-form-item label="测试类型">
          {{staffData.surveyType}}
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
    <el-pagination
      background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="staffCurrentPage"
      :page-sizes="[7 , 16, 32, staffTotalCount]"
      :page-size="staffPageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="staffTotalCount">
    </el-pagination>
  </div>
</template>

<script>
import { mapState, mapMutations } from 'vuex';

export default {
  name: 'Summary',
  data() {
    return {
      switchValue: true,
      disability: false,
      tableData: [],
      tableLoading: true,
      staffPageSize: 7,
      staffCurrentPage: 1,
      staffTotalCount: 0,
      evalTagShow: true,
      scoreTagShow: false,
      refreshLoading: false,
      index: 1,
      dialogVisible: false,
      logoutVisible: false,
      staffData: [],
      labelPosition: 'left',
      // 查询条件
      search: {
        option: [
          {
            value: '1',
            label: '姓名',
          },
          {
            value: '2',
            label: '标签',
          },
        ],
        select: '1',
        value: '',
      },
      tempData: [],
      // 存放满足查询条件的数据
      result: [],
    };
  },
  computed: {
    ...mapState(['token']),
  },
  created() {
    this.getData();
    // console.log(this.token);
    const token = this.$utils.getCache('token');
    if (token) {
      this.setToken(token);
    }
  },
  methods: {
    refresh() { // 刷新函数
      this.refreshLoading = true;
      this.getData();
      this.staffCurrentPage = 1;
    },
    getData() { // 数据获取函数
      this.tableLoading = true;
      this.$http.get('summary', {
        headers: {
          Authorization: this.$store.state.token,
        },
      })
        .then((response) => {
          if (response) {
            console.log(response);
            this.pushData(response);
            this.tableLoading = false;
            this.refreshLoading = false;
          }
        })
        .catch((error) => {
          // console.log(error);
          if (error.response.data.message) {
            this.$message.error(error.response.data.message);
          } else {
            this.$message.error('服务器连接错误！');
            this.refreshLoading = false;
          }
        });
    },
    pushData(data) { // 数据渲染函数
      this.tableData.splice(0, this.tableData.length);
      this.tableData = this.$utils.manageData(data);
      this.tempData = this.tableData;
      // console.log(this.tableData);
      this.staffTotalCount = data.length;
    },
    handleSizeChange(val) { // 改变每页显示条数
      this.staffPageSize = val;
    },
    handleCurrentChange(val) { // 改变当前页码
      this.staffCurrentPage = val;
    },
    getEval(level) { // 获取评价
      if (level === 3) {
        return '优秀';
      }
      if (level === 2) {
        return '自然';
      }
      if (level === 1) {
        return '避免';
      }
      if (level === 0) {
        return '严重';
      }
      return '暂无';
    },
    getType(level) { // 获取标签类型
      if (level === 3) {
        return 'primary';
      }
      if (level === 2) {
        return 'success';
      }
      if (level === 1) {
        return 'warning';
      }
      if (level === 0) {
        return 'danger';
      }
      return 'info';
    },
    changeSwitch(state) { // 切换显示分数或评价
      this.disability = true;
      // console.log(state);
      if (state === false) {
        this.evalTagShow = false;
        setTimeout(() => {
          this.scoreTagShow = true;
          if (this.scoreTagShow === true) {
            this.disability = false;
          }
        }, 390);
      } else {
        this.scoreTagShow = false;
        setTimeout(() => {
          this.evalTagShow = true;
          if (this.evalTagShow === true) {
            this.disability = false;
          }
        }, 390);
      }
    },
    getDetails(index) { // 详情函数
      // console.log(index);
      this.dialogVisible = true;
      this.index = index - 1;
      this.staffData = this.tempData[this.index];
    },
    searchData() { // 搜索函数
      this.staffCurrentPage = 1;
      this.staffPageSize = 7;
      // console.log(this.tempData);
      // console.log(this.search.value);
      this.result = [];
      if (this.search.select === '1') {
        // console.log(this.search.select);
        this.tempData.forEach((element, index) => {
          if (element.staffName.indexOf(this.search.value) >= 0) {
            this.result.push(this.tempData[index]);
          }
        });
      } else if (this.search.select === '2') {
        // console.log(this.search.select);
        this.tempData.forEach((element, index) => {
          element.tags.forEach((elem) => {
            if (elem.indexOf(this.search.value) >= 0) {
              this.result.push(this.tempData[index]);
            }
          });
        });
      } else {
        this.result = this.tempData;
      }
      // console.log(this.result);
      this.tableData = this.result;
      this.staffTotalCount = this.tableData.length;
    },
    logout() { // 退出函数
      this.logoutVisible = false;
      this.$http.delete('logout', {
        headers: {
          Authorization: this.$store.state.token,
        },
      })
        .then((response) => {
          if (response) {
            this.$message({
              message: response.data.message,
              type: 'success',
              duration: 1500,
            });
            const token = '';
            localStorage.TOKEN = token;
            this.setToken(token);
            this.$store.commit('setToken', token);
            this.$router.push({
              name: 'login',
            });
          }
        })
        .catch((error) => {
          // console.log(error);
          if (error.response.data.message) {
            this.$message.error(error.response.data.message);
          } else {
            this.$message.error('服务器连接错误！');
            this.refreshLoading = false;
          }
        });
    },
    ...mapMutations(['setToken']),
  },
};
</script>

<style lang="less">
  .management {
    width: 100%;
    height: 105px;
    // border: 1px solid #000;
    // max-height: 100%;
    // position:absolute;
    // z-index:20000;
    // left:0;
    // top:0;
    // background-image: url(../../assets/management.png);
    // background-repeat:no-repeat;
    // background-position: center 0;
    // background-attachment: fixed;
  }
  .refresh {
    // border: 1px solid #000;
    float: right;
    margin-right: 20px;
    margin-top: 5px;
  }
  .logout {
    float: right;
    margin-right: 10px;
    margin-top: 5px;
  }
  .el-table td.iscenter, .el-table th.is-center {
    background: #f0fbff;
    color: #636262;
  }
  .el-switch {
    // border: 1px solid #000;
    height: 40px;
    margin-left: 80px;
  }
  // .el-pagination button, .el-pagination span:not([class*=suffix]) {
  //   color: #fff;
  // }
  .el-input-group {
    margin-bottom: 5px;
    margin-left: 35px;
  }
  .el-dialog__body {
    line-height: 25px;
  }
  .el-tag {
    width: 80%;
  }
  .scoreTag {
    margin: 0 auto;
  }
  .el-pagination {
    // margin-top: 20px;
    // border: 1px solid #000;
    line-height: 32px;
    white-space: nowrap;
    padding: 3px 5px;
    color: #303133;
    font-weight: 700;
    text-align: center;
    background: #a3eaee;
  }
  .el-select .el-input {
    width: 100px;
  }
  .input-with-select {
    width: 400px;
  }
  .el-form-item {
    margin-bottom: 0px;
  }
</style>
