<template>
  <div class="line-title">
    <span> 总共 {{ list.length }} 条数据， 当滚到最后一条数据时，加载下一页100条数据 </span>
    <span> 44条数据 from {{ from }} to {{ to }} </span>
    <span>当前已经滚动到 {{ displayCount }}</span>
  </div>
  <div class="virtual-list" ref="virtualList" @scroll="handleScroll">
    <ul id="ulBox" :style="{paddingTop: lineTopHeight + 'px', paddingBottom: lineBottomHeight + 'px'}">
      <li v-for="item in previewList" :key="item.title">
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script>
window.COUNT = 0
export default {
  data() {
    return {
      list: [], // 所有的item
      previewList: [], // 44 条数据， above 22 ,screen 11, below: 11
      from: 0,
      to: 44,
      displayCount: 0, // 当前可视区域内的第一条数据
      lineTopHeight: 0, //  lineTopHeight + 44条数据的高度 + lineBottomHeight = list总的item高度
      lineBottomHeight: 0,
      canLoadMore: true,
    }
  },
  mounted () {
    this.loadData()
  },
  methods: {
    loadData(from = 0, to = 44) {
      if (!this.canLoadMore) return
      this.canLoadMore = false
      // 模拟加载100条数据
      for (let i = 0; i < 100; i++) {
        this.list.push({ 
          title: 'item' + COUNT++
        })
      }
      this.from = from
      this.to = to
      this.canLoadMore = true
      this.resetPreviewList(from, to)
    },
    resetPreviewList (from, to) {
      this.previewList = []
      for (; from < to; from++) {
        this.previewList.push(this.list[from])
      }
    },
    handleScroll () {
      const scrollTop = this.$refs.virtualList.scrollTop
      const clientHeight = this.$refs.virtualList.offsetHeight
      const lis = document.getElementsByTagName('li')
      if (scrollTop / lis[0].offsetHeight - Math.floor(scrollTop / lis[0].offsetHeight) > 0.5) {
        this.displayCount = Math.ceil(scrollTop / lis[0].offsetHeight)
      } else {
        this.displayCount = Math.floor(scrollTop / lis[0].offsetHeight)
      }

      const ulElm = document.getElementById('ulBox')
      if (this.to === this.list.length && ulElm.offsetHeight - scrollTop - clientHeight < 10) { // 加载下一个100条数据
        this.loadData(this.from, this.to)
        return
      }

      let _from = this.displayCount - 22 // 22表示上缓冲区的数量
      if (_from < 0) {
        _from = 0
      }
      let _to = _from + 44 // 44 表示数量总个数
      if (_to > this.list.length) {
        _to = this.list.length
      }
      this.from = _from
      this.to = _to
      // 填充ul的高度
      this.lineTopHeight = _from * 44
      this.lineBottomHeight = (this.list.length - _to) * 44
      this.resetPreviewList(_from, _to)
    },

  }
}
</script>

<style>
.virtual-list {
  width: 464px;
  height: 480px;
  border: 1px solid #ccc;
  margin: 0 auto;
  overflow: auto;
}
ul {
  margin: 0;
  padding: 0;
}
li {
  height: 44px;
  line-height: 44px;
  text-decoration: none;
  padding-left: 15px;
  border-bottom: 1px solid #ddd;
}
li:nth-last-child(1) {
  border-bottom: none;
}
.line-title {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
</style>