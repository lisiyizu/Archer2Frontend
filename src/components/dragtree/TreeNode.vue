<!--
<template lang="pug">
.tree-node(
  :class="[data.active ? store.activatedClass : '', data.open ? store.openedClass : '', data.class]"
  :style="data.style"
  :id="data._id"
)
  .tree-node-inner-back(v-if="!isRoot" :style="[innerBackStyle, data.innerBackStyle]" :class="[data.innerBackClass]")
    .tree-node-inner(:style="[data.innerStyle]" :class="[data.innerClass]")
      slot(:data="data" :store="store" :vm="vm")
  .tree-node-children(v-if="childrenVisible")
    TreeNode(v-for="child in data.children" :key="child._id"
      :data="child" :store="store" :level="childrenLevel"
    )
      template(slot-scope="props")
        slot(:data="props.data" :store="props.store" :vm="props.vm")
</template>
-->

<template>
  <div class="tree-node" :class="[data.active ? store.activatedClass : '', data.open ? store.openedClass : '', data.class]" :style="data.style" :id="data._id">
    <div class="tree-node-inner-back" v-if="!isRoot" :style="[innerBackStyle, data.innerBackStyle]" :class="[data.innerBackClass]">
      <div class="tree-node-inner" :style="[data.innerStyle]" :class="[data.innerClass]">
        <slot :data="data" :store="store" :vm="vm"></slot>
      </div>
    </div>
    <div class="tree-node-children" v-if="childrenVisible">
      <TreeNode v-for="child in data.children" :key="child._id" :data="child" :store="store" :level="childrenLevel">
        <template slot-scope="props">
          <slot :data="props.data" :store="props.store" :vm="props.vm"></slot>
        </template>
      </TreeNode>
    </div>
  </div>
</template>

<script>
import * as th from 'tree-helper'

export default {
  name: 'TreeNode',
  props: {
    data: {},
    store: {},
    level: {default: 0}, // readonly
  },
  data() {
    return {
      vm: this,
    }
  },
  computed: {
    childrenLevel() {
      return this.level + 1
    },
    isRoot() {return this.data.isRoot},
    childrenVisible() {
      const {data} = this
      return this.isRoot || data.children && data.children.length && data.open
    },
    innerBackStyle() {
      const r = {
        marginBottom: this.store.space + 'px'
      }
      if (!this.isRoot && this.level > 1) {
        r.paddingLeft = (this.level - 1) * this.store.indent + 'px'
      }
      return r
    },
  },
  watch: {
    data: {
      immediate: true,
      handler(data) {
        if (data) {
          data._vm = this
          if (!data._treeNodePropertiesCompleted && !data.isRoot) {
            this.store.compeleteNode(data, this.$parent.data)
          }
        }
      }
    },
  },
  // methods: {},
  // created() {},
  // mounted() {},
}
</script>
