<template>
  <div id="app">
    <a-input placeholder="请输入任务" class="my_ipt" :value="inputValue" @change="handleInputChange" />
    <a-button type="primary" @click="addItemToList">添加事项</a-button>

    <a-list bordered :dataSource="infoList" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <!-- 复选框 -->
        <!-- checked 属性，用 v-bind 动态绑定属性 -->
        <a-checkbox :checked="item.done" @change="(e) => {cbStatusChange(e, item.id)}">{{item.info}}</a-checkbox>
        <!-- 删除链接 -->
        <a slot="actions" @click="removeItemById(item.id)">删除</a>
      </a-list-item>

      <!-- footer 区域 -->
      <div slot="footer" class="footer">
        <!-- 未完成的任务个数 -->
        <span>{{unDoneLength}}条剩余</span>
        <!-- 操作按钮 -->
        <a-button-group>
          <a-button :type="viewKey === 'all' ? 'primary':'default'" @click="changeList('all')">全部</a-button>
          <a-button :type="viewKey === 'undone' ? 'primary':'default'" @click="changeList('undone')">未完成</a-button>
          <a-button :type="viewKey === 'done' ? 'primary':'default'" @click="changeList('done')">已完成</a-button>
        </a-button-group>
        <!-- 把已经完成的任务清空 -->
        <a @click="clean">清除已完成</a>
      </div>
    </a-list>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'

export default {
  name: 'app',
  data() {
    return {}
  },
  created() {
    this.$store.dispatch('getList')
  },
  computed: {
    ...mapState(['inputValue', 'viewKey']),
    ...mapGetters(['unDoneLength', 'infoList'])
  },
  methods: {
    handleInputChange(e) {
      // 百度：每次触发DOM事件时会产生一个事件对象（也称event对象），此处的参数e接收事件对象。而事件对象也有很多属性和方法，其中target属性是获取触发事件对象的目标，也就是绑定事件的元素，e.target表示该DOM元素，然后在获取其相应的属性值。
      console.log(e.target.value)
      this.$store.commit('setInputValue', e.target.value)
    },
    addItemToList() {
      // if (this.$store.inputValue.trim().length <= 0) {
      //   // 不要忘记 return !!!
      //   return this.$message.warning('输入框不能为空')
      // }
      this.$store.commit('addItem')
    },
    removeItemById(id) {
      console.log(id)
      this.$store.commit('removeItem', id)
    },
    // 监听复选框中状态变化的 事件
    cbStatusChange(e, id) {
      // 通过 e.target.checked 可以接收到最新的选中状态
      // console.log(e.target.checked)
      // console.log(id)
      const param = {
        id: id,
        status: e.target.checked
      }
      this.$store.commit('changeStatus', param)
    },
    // 清除已完成的任务
    clean() {
      this.$store.commit('cleanDone')
    },
    // 修改页面上展示的列表数据
    changeList(key) {
      // console.log(key)
      this.$store.commit('changeViewKey', key)
    }
  }
}
</script>

<style scoped>
#app {
  padding: 10px;
}

.my_ipt {
  width: 500px;
  margin-right: 10px;
}

.dt_list {
  width: 500px;
  margin-right: 10px;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
