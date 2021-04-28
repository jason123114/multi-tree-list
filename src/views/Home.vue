<template>
  <div class="app-container">
    <div class="container-left">
      <treeList :tree-list="list" :tree-info-obj="treeInfoObj" :show-right-menu="true" :show-line="true" :show-check="true" :default-props="treeProps" :child-node-opt="childNodeOpt" @clickCurrTree="getCurrTreeInfo" @getChildNode="getChildNode" @botSearch="botSearch">
        <template v-slot:CustomMenu="{row}">
          <ul>
            <li v-for="(item,index) in menuList" :key="index" @click="item.contextMenuMethod(item, row)">{{ item.name }}</li>
          </ul>
        </template>
      </treeList>
    </div>
    <div class="container-right">{{ contextMenuClickRelevant }}</div>
  </div>
</template>

<script>
import treeList from '@/components/tree-list'

export default {
  name: 'Home',
  components: {
    treeList
  },
  data() {
    return {
      treeInfoObj: {
        treeTitle: 'xxxx集团公司资源管理'
      },
      childNodeOpt: {
        leafOnly: true, // 是否只是 子节点
        includeHalfChecked: false // 是否包含半选节点
      },
      treeProps: {
        icon: 'resIcon',
        color: 'resColor',
        nodeKey: 'id',
        children: 'children',
        label: 'label'
      },
      list: [
        {
          label: 'xxxx集团公司',
          id: '0',
          resIcon: 'el-icon-folder-opened',
          resColor: '#005bac',
          children: [
            {
              label: '集团总公司',
              id: '1',
              resIcon: 'el-icon-folder-opened',
              resColor: '#005bac',
              children: [
                { label: '技术研发本部', id: '2', resIcon: 'el-icon-folder-opened', resColor: '#005bac', children: [
                  { label: 'App处', id: '2-1', resIcon: 'el-icon-folder-opened', resColor: '#f80' },
                  { label: '前端处', id: '2-2', resIcon: 'el-icon-folder-opened', resColor: '#f80' },
                  { label: 'Java处', id: '2-3', resIcon: 'el-icon-folder-opened', resColor: '#f80' },
                  { label: '产品处', id: '2-4', resIcon: 'el-icon-folder-opened', resColor: '#f80' }
                ] },
                { label: '人力资源部', id: '3', resIcon: 'el-icon-folder-opened', resColor: '#005bac', children: [
                  { label: '薪资组', id: '3-1', resIcon: 'el-icon-folder-opened', resColor: '#f80' },
                  { label: '招聘组', id: '3-2', resIcon: 'el-icon-folder-opened', resColor: '#f80' }
                ] }
              ]
            },
            { label: '合肥分公司', id: '4', resIcon: 'el-icon-bell', resColor: '#f80' },
            { label: '杭州分公司', id: '5' },
            { label: '宁波分公司', id: '6' },
            { label: '上海分公司', id: '7' },
            { label: '苏州分公司', id: '8' }
          ]
        }
      ],
      menuList: [
        { name: '增加组织', value: 'add', contextMenuMethod: this.menuClick }, { name: '删除组织', value: 'del', contextMenuMethod: this.menuClick }, { name: '变更隶属关系', value: 'edit', contextMenuMethod: this.menuClick }, { name: '合并组织', value: 'merge', contextMenuMethod: this.menuClick },
        { name: '恢复组织', value: 'recover', contextMenuMethod: this.menuClick }, { name: '下级排序', value: 'sort', contextMenuMethod: this.menuClick }
      ],
      contextMenuClickRelevant: '树节点中的右击菜单'
    }
  },
  methods: {
    getCurrTreeInfo(data) {
      console.log(data)
    },
    // 自定义菜单点击
    menuClick(item, row) {
      this.contextMenuClickRelevant = ` 需要操作的tree节点: ${row.label} (id: ${row.id}) 事件：${item.name} (${item.value})`
    },
    // 获取选中节点
    getChildNode(treeNode) {
      console.log(treeNode)
    },
    // 底部搜索
    botSearch(data) {
      console.log(data)
    }
  }
}
</script>
<style scoped>
.app-container{
  display: flex;
}
.app-container .container-left{
  width: 350px;height: 100%;
}
</style>
