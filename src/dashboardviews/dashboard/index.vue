<template>
  <div class="dashboard-editor-container">
    <el-card class="welcome-card">
      <el-row :gutter="40">
        <el-card shadow="never" style="margin: 0 20px;">
          <el-col :md="12" :sm="12" :lg="12">
            <div class="news-text">
              <el-card class="box-card">
                <div slot="header" class="box-text">
                  <span>文章总量</span>
                </div>
                <div class="box-num">{{totalArticlesNumber}}</div>
              </el-card>
              <el-card class="box-card">
                <div slot="header" class="box-text">
                  <span>评论总量</span>
                </div>
                <div class="box-num">12</div>
              </el-card>
            </div>
          </el-col>
        </el-card>
      </el-row>
    </el-card>
    <el-row :gutter="10">

      <el-col :xs="24" :sm="24" :lg="12">
        <el-card>
          <el-row justify = "center">最新文章</el-row>
          <el-table
            v-loading="listLoading"
            :data="articleList"
            element-loading-text="Loading"
            border
            fit
            size="mini"
            highlight-current-row>
            <el-table-column label="标题">
              <template slot-scope="scope">
                {{ scope.row.title }}
              </template>
            </el-table-column>
            <el-table-column label="作者" width="110" align="center">
              <template slot-scope="scope">
                <span>{{ scope.row.author }}</span>
              </template>
            </el-table-column>
            <el-table-column label="分类" align="center">
              <template slot-scope="scope">
                <span>{{scope.row.categoryName}}</span>
              </template>
            </el-table-column>
            <el-table-column label="标签" align="center">
              <template slot-scope="scope">
                <!--<span>{{scope.row.tagNames}}</span>-->
                <a v-for="tagName in scope.row.tagNames" href="#">{{tagName}}, </a>
              </template>
            </el-table-column>
            <el-table-column class-name="status-col" label="状态" width="110" align="center">
              <template slot-scope="scope">
                <el-tag :type="scope.row.state === 'release' ? 'success' : 'warning'">
                  {{ scope.row.state === 'release' ? '已发布' : '未发布' }}
                </el-tag>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <el-card>
          <el-row>最新评论</el-row>
          <el-table
            v-loading="listLoading"
            :data="commentList"
            element-loading-text="Loading"
            border
            fit
            size="mini"
            highlight-current-row>
            <el-table-column label="留言人" width="100">
              <template slot-scope="scope">
                {{ scope.row.name }}
              </template>
            </el-table-column>
            <el-table-column label="留言内容">
              <template slot-scope="scope">
                {{ scope.row.content }}
              </template>
            </el-table-column>
            <el-table-column label="网址" width="150">
              <template slot-scope="scope">
                <a :href="scope.row.url" target="_blank" style="color: #409EFF">{{scope.row.url}}</a>
              </template>
            </el-table-column>
            <el-table-column label="IP" width="120">
              <template slot-scope="scope">
                {{ scope.row.ip }}
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import {getCommentList} from '@/api/comment'
  import {getArticlePage} from '@/api/article'
  import {mapGetters} from 'vuex'
  import GithubCorner from '@/components/GithubCorner/index'
  import PanThumb from '@/components/PanThumb/index'

  export default {
    name: 'DashboardAdmin',
    components: {
      GithubCorner,
      PanThumb
    },
    computed: {
      ...mapGetters([
        'sidebar',
        'avatar',
        'name'
      ])
    },
    data() {
      return {
        commentList: null,
        articleList: null,
        listLoading: true,
        listQuery: {
          page: 1,
          limit: 10
        },
        activeName: 'first',
        welcomeMessage: null
      }
    },
    created() {
      this.fetchData()
      this.init()
    },
    methods: {
      fetchData() {
        this.listLoading = true
        getArticlePage({page: 1, limit: 8}).then(res => {
          this.articleList = res.data.elements
          this.currentArticlePage = res.data.currentPage
          this.articlePages = res.data.totalPages
          this.totalArticlesNumber = res.data.totalNumber
          this.listLoading = false
        })
        getCommentList({}, this.listQuery).then(response => {
          this.commentList = response.data.rows
          this.listLoading = false
        })
      },

      init() {
        var hour = new Date().getHours();
        var time = hour < 6 ? '早上好' : (hour <= 11 ? '上午好' : (hour <= 13 ? '中午好' : (hour <= 18 ? '下午好' : '晚上好')));
        var welcomeArr = [
          '喝杯咖啡休息下吧☕',
          '要不要和朋友打局LOL',
          '今天又写了几个Bug呢',
          '今天在群里吹水了吗',
          '今天吃了什么好吃的呢',
          '今天您微笑了吗😊',
          '今天帮助别人了吗',
          '准备吃些什么呢',
          '周末要不要去看电影？'
        ];
        var index = Math.floor((Math.random() * welcomeArr.length));
        this.welcomeMessage = time + '，<a style="color: #5a8bff;">' + this.name + '</a>，' + welcomeArr[index];
      }

    }
  }
</script>

<style lang="scss" scoped>
  .dashboard-editor-container {
    padding: 32px;
    background-color: rgb(240, 242, 245);
    position: relative;

    .github-corner {
      position: absolute;
      top: 0px;
      border: 0;
      right: 0;
    }

    .welcome-card {
      margin-bottom: 10px;
    }

    .welcome-text {
      color: rgba(0, 0, 0, 0.45);
      padding: .2rem;
      display: inline-block;

      .user-info {
        display: inline-block;
        vertical-align: top;
        padding: 25px;

        .random-message {
          font-size: 1rem;
          margin-bottom: 0.8rem;
          max-width: 21rem;
        }
      }
    }

    .news-text {
      float: right;
      padding: .2rem;

      .box-card {
        padding: 0 10px;
        display: inline-block;
        border: none;
        box-shadow: none !important;

        /deep/ .el-card__header {
          border: none !important;
        }

        .box-num {
          font-size: 20px;
          font-weight: 700;
          color: #20a0ff;
        }
      }
    }

    .project-card {
      .project-item {
        padding: .5rem 0.7rem;
        border: 1px solid #f1f1f1;

        .project-name {
          width: 100%;
          height: 24px;

          a {
            color: #42b983;
            font-size: .9rem;
            font-weight: 600;
          }
        }

        .project-desc {
          line-height: 24px;
          color: rgba(0, 0, 0, 0.45);
        }
      }
    }

    .info-card {
      margin-top: 10px;

      .info-item-card {
        /deep/ .el-card {
          border-radius: 0;
          border: 1px solid #e6e6e6;
        }

        .info-header {
          display: inline-block;
          width: 80px;
        }

        .info-text {
          display: inline-block;

          a {
            color: #5a8bff;
          }
        }
      }
    }
  }

  @media (max-width: 1024px) {
    .chart-wrapper {
      padding: 8px;
    }
  }
</style>
