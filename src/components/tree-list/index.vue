<template>
  <div class="tree-box">
    <div class="left-tree">
      <div class="tree-input">
        <el-input
          v-model.trim="topInput"
          clearable
          suffix-icon="el-icon-search"
          placeholder="请输入内容搜索"
        />
      </div>
      <p v-if="showRightMenu" class="tips">提示：右键树节点进行操作</p>
      <div v-if="treeInfoObj.treeTitle && treeInfoObj.treeTitle!=''" class="tree-title">
        {{ treeInfoObj.treeTitle }}
      </div>
      <div class="tree-content mytree">
        <el-tree
          ref="tree"
          :show-checkbox="showCheck"
          :indent="showLine?0:15"
          :class="showLine?'tree-line':''"
          :data="treeList"
          :props="defaultProps"
          :node-key="defaultProps.nodeKey"
          default-expand-all
          :filter-node-method="filterNode"
          :expand-on-click-node="false"
          @node-click="handleNodeClick"
          @node-contextmenu="openMenu"
          @check="getTreeNode"
        >
          <span slot-scope="{ node, data }" class="custom-tree-node">
            <span :id="node">
              <i v-if="showIcon" :class="data[defaultProps.icon] || iconInfo.icon" :style="{'margin': '0 3px',color:data[defaultProps.color] || iconInfo.color,'font-weight':'600'}" /> {{ data[defaultProps.label] }}
            </span>
          </span>
        </el-tree>
      </div>
      <div v-if="showBottom" class="tree-search">
        <el-input v-model.trim="botInput" placeholder="请输入内容" class="input-with-select" clearable>
          <el-button slot="append" @click.native="searchName">快速搜索</el-button>
        </el-input>
      </div>
      <div v-if="showRightMenu" class="custom-right-menu">
        <div v-if="showMenuModal" :style="{left:left+'px',top:top+'px'}" class="contextmenu">
          <slot name="CustomMenu" :row="menuRow" />
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'TreeList',
  props: {
    /**
     * 数据列表
    */
    treeList: {
      type: Array,
      default: () => []
    },
    // 是否显示文字左边图片
    showIcon: {
      type: Boolean,
      default: true
    },
    // 是否显示底部搜索
    showBottom: {
      type: Boolean,
      default: false
    },
    // 是否启用右键菜单
    showRightMenu: {
      type: Boolean,
      default: false
    },
    // 是否显示提示连接线
    showLine: {
      type: Boolean,
      default: false
    },
    // 是否显示多选
    showCheck: {
      type: Boolean,
      default: false
    },
    // 子节点数据获取 配置
    childNodeOpt: {
      type: Object,
      default: () => ({
        leafOnly: true,
        includeHalfChecked: false
      })
    },
    /*
     * 组件参数
     * treeTitle：树组件标题
    */
    treeInfoObj: {
      type: Object,
      default: () => ({
        treeTitle: ''
      })
    },
    /*
     * 属性键值对
     * icon： 图片属性名
     * color: 图片颜色属性名
     * nodeKey： 主键属性名
     * children: 子节点属性名
     * label：显示的文字属性名
    */
    defaultProps: {
      type: Object,
      default: () => ({
        icon: 'icon',
        color: 'color',
        nodeKey: 'id',
        children: 'children',
        label: 'label'
      })
    },
    iconInfo: { // 定义 icon  颜色
      type: Object,
      default: () => ({
        icon: 'el-icon-folder-opened',
        color: '#005bac'
      })
    }
  },
  data() {
    return {
      topInput: '',
      botInput: '',
      showMenuModal: false,
      top: 0,
      left: 0,
      menuRow: undefined
    }
  },
  watch: {
    showMenuModal(value) {
      if (value) {
        document.body.addEventListener('click', this.closeMenu)
      } else {
        document.body.removeEventListener('click', this.closeMenu)
      }
    },
    topInput(val) {
      this.$refs.tree.filter(val)
    },
    botInput(val) {
      this.$refs.tree.filter(val)
    }
  },
  created() {

  },

  methods: {
    handleNodeClick(data) {
      this.showMenuModal = false
      this.$emit('clickCurrTree', data)
    },
    // 底部快速搜索 botSearch
    searchName() {
      this.$emit('botSearch', this.botInput)
    },
    // 自定义右键菜单
    openMenu(e, data) {
      this.menuRow = data
      if (this.showRightMenu) {
        const x = e.clientX + 20
        const y = e.clientY
        this.top = y
        this.left = x
        this.showMenuModal = true
      }
    },
    closeMenu() {
      this.showMenuModal = false
    },
    // tree 过滤
    filterNode(val, data) {
      if (!val) return true
      return data[this.defaultProps.label].indexOf(val) !== -1
    },
    // 节点选择
    getTreeNode() {
      const tree = this.$refs.tree.getCheckedNodes(this.childNodeOpt['leafOnly'], this.childNodeOpt['includeHalfChecked'])
      this.$emit('getChildNode', tree)
    }
  }
}

</script>
<style scoped>
.tree-box{
  position: relative;width: 100%;
}
.left-tree{
  width: 100%;min-height: 600px;box-sizing: border-box;border: 1px solid #dedede;border-radius: 5px;background: #fff;
  padding: 0 0 50px 0;
  overflow-y: auto;max-height: calc(100vh - 100px);
}
p.tips{
  padding:8px !important;color: #999;font-size: 12px;
}
.left-tree::-webkit-scrollbar{
  width:5px;
  height:5px;
}
.left-tree::-webkit-scrollbar-track{
  background: rgb(239, 239, 239);
  border-radius:2px;
}
.left-tree::-webkit-scrollbar-thumb{
  background: #bfbfbf;
  border-radius:5px;
}
.left-tree::-webkit-scrollbar-thumb:hover{
  background: #333;
}
.left-tree::-webkit-scrollbar-corner{
  background: #179a16;
}

.left-tree .tree-input{
    padding: 8px;box-sizing: border-box;background: #fff;width: 100%;
}
.left-tree .tree-search{
  background: #f1f5f8;height: 40px;box-sizing: border-box;padding-top: 4px;margin-top: 10px;width: 100%;
  position: absolute;bottom: 0;left: 0;
}
.left-tree .tree-title{
  text-align: left;font-size: 14px;color: #333;font-weight: 600;line-height: 24px;box-sizing: border-box;padding: 6px 8px;
}
.left-tree .tree-content{
  padding: 0 2px;
}
.left-tree .el-tree /deep/ .is-current>.el-tree-node__content{
  background: #98CAF8 !important;
  color: #fff !important;
}
.contextmenu {
  margin: 0;
  background: #fff;
  z-index: 3000;
  position: fixed;
  list-style-type: none;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 500;
  color: #333;
  text-align: center;
  box-shadow: 0px 2px 5px 3px rgba(0, 0, 0, 0.3);
}
.contextmenu ul{
  padding: 0;
}
.contextmenu li {
  list-style: none;
  margin: 0;
  padding: 7px 25px;
  cursor: pointer;
}
.contextmenu li:hover {
  background-color: rgb(3, 125, 243);;
  color: white;
}
</style>
<style lang="less">
.tree-line{
  .el-tree-node {
    position: relative;
    padding-left: 15px;
  }
  .el-tree-node__children {
    padding-left: 15px;
  }
  .el-tree-node::before {
    content: "";
    height: 100%;
    width: 1px;
    position: absolute;
    left: -3px;
    top: -26px;
    border-width: 1px;
    border-left: 1px dashed #dedede;
  }
  .el-tree-node:last-child::before {
    height: 38px;
  }
  .el-tree-node::after {
    content: "";
    width: 20px;
    height: 20px;
    position: absolute;
    left: -3px;
    top: 12px;
    border-width: 1px;
    border-top: 1px dashed #dedede;
  }
  & > .el-tree-node::after {
    border-top: none;
  }
  & > .el-tree-node::before {
    border-left: none;
  }
}
</style>

