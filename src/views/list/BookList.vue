<template>
  <div>
    <a-card :bordered="false">
      <a-row>
        <a-col :sm="8" :xs="24">
          <head-info title="图书总数" content="10000本" :bordered="true"/>
        </a-col>
        <a-col :sm="8" :xs="24">
          <head-info title="图书平均价格" content="$55.5" :bordered="true"/>
        </a-col>
        <a-col :sm="8" :xs="24">
          <head-info title="本周新增" content="10个"/>
        </a-col>
      </a-row>
    </a-card>

    <a-card
      style="margin-top: 24px"
      :bordered="false"
      title="图书列表">

      <div slot="extra">
        <a-radio-group v-model="status">
          <a-radio-button value="all">全部</a-radio-button>
          <a-radio-button value="processing">进行中</a-radio-button>
          <a-radio-button value="waiting">等待中</a-radio-button>
        </a-radio-group>
        <a-input-search style="margin-left: 16px; width: 272px;" />
      </div>

      <div class="operate">
        <a-button type="dashed" style="width: 100%" icon="plus" @click="$refs.taskForm.add()">添加</a-button>
      </div>

      <a-list size="large" :pagination="{showSizeChanger: true, showQuickJumper: true, pageSize: 5, total: 50}">
        <a-list-item :key="index" v-for="(item, index) in data">
          <a-list-item-meta :description="item.bookName">
            <a-avatar slot="avatar" size="large" shape="square" :src="item.bookName"/>
            <a slot="title">{{ item.bookName }}</a>
          </a-list-item-meta>
          <div slot="actions">
            <a @click="edit(item)">编辑</a>
          </div>
          <div slot="actions">
            <a-dropdown>
              <a-menu slot="overlay">
                <a-menu-item><a>编辑</a></a-menu-item>
                <a-menu-item><a>删除</a></a-menu-item>
              </a-menu>
              <a>更多<a-icon type="down"/></a>
            </a-dropdown>
          </div>
          <div class="list-content">
            <div class="list-content-item">
              <span>类别</span>
              <p>{{ item.bookKindStr }}</p>
            </div>
            <div class="list-content-item">
              <span>出版时间</span>
              <p></p>
            </div>
            <div class="list-content-item">
             </div>
          </div>
        </a-list-item>
      </a-list>
    </a-card>
  </div>
</template>

<script>
// import HeadInfo from '@/components/tools/HeadInfo'
import TaskForm from './modules/TaskForm'
import getBookList from '@/graphql/BookQuery.gql'
export default {
  name: 'BookList',
  components: {
    // HeadInfo,
    TaskForm
  },
  data () {
    return {
      data: [],
      status: 'all'
    }
  },
  async created () {
    const rsp = await this.$apollo.query({
      query: getBookList
    })
    const { books } = rsp.data.list
    this.data = books
    console.log(rsp)
  },
  methods: {
    edit (record) {
      console.log('record', record)
      // mockdata
      record.taskName = '测试'
      // mockend
      this.$dialog(TaskForm,
        // component props
        {
          record
        },
        // modal props
        {
          title: '操作',
          width: 700,
          centered: true,
          maskClosable: false
        })
    }
  }
}
</script>

<style lang="less" scoped>
    .ant-avatar-lg {
        width: 48px;
        height: 48px;
        line-height: 48px;
    }

    .list-content-item {
        color: rgba(0, 0, 0, .45);
        display: inline-block;
        vertical-align: middle;
        font-size: 14px;
        margin-left: 40px;
        span {
            line-height: 20px;
        }
        p {
            margin-top: 4px;
            margin-bottom: 0;
            line-height: 22px;
        }
    }
</style>
