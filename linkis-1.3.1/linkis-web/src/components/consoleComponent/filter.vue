<!--
  ~ Licensed to the Apache Software Foundation (ASF) under one or more
  ~ contributor license agreements.  See the NOTICE file distributed with
  ~ this work for additional information regarding copyright ownership.
  ~ The ASF licenses this file to You under the Apache License, Version 2.0
  ~ (the "License"); you may not use this file except in compliance with
  ~ the License.  You may obtain a copy of the License at
  ~
  ~   http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing, software
  ~ distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and
  ~ limitations under the License.
  -->

<template>
  <div
    class="we-filter-view">
    <virtual-list
      ref="fieldList"
      wtag="ul"
      :size="size"
      :remain="remain"
      :item="item"
      :itemcount="total"
      :itemprops="getItemprops"/>
  </div>
</template>
<script>
import virtualList from '@/components/virtualList';
import item from './filterItem.vue'
export default {
  components: {
    virtualList,
  },
  props: {
    headRows: {
      type: Array,
      default: () => [],
    },
    height: { // tree height(树高度)
      type: Number,
      require: true
    },
  },
  data() {
    return {
      total: this.headRows.length,
      remain: 0,
      size: 28,
      item
    }
  },
  watch: {
    height() {
      this.layout()
    }
  },
  mounted(){
    this.layout()
  },
  methods: {
    hanlderCheck(index) {
      let item = this.headRows[index]
      let count = 0;
      this.headRows.forEach((item) => {
        if (item.checked) {
          count += 1;
        }
      });
      if (count > 1 || (count === 1 && !item.checked)) {
        this.$emit('on-check', item);
      } else {
        this.$Message.warning('请至少勾选一个字段');
      }
    },
    getItemprops(index) {
      return {
        key: index,
        props: {
          item: this.headRows[index]
        },
        nativeOn: {
          click: (ev) => {
            ev.stopPropagation()
            this.hanlderCheck(index)
          },
        }
      }
    },
    layout() {
      let rows =  Math.floor((this.height - 70) / this.size) // 列删选浮框在结果集表格上，高度不包括分页、表头
      this.total = this.headRows.length
      this.remain = this.total > rows ? rows : this.total
      this.$refs.fieldList.forceRender();
    }
  },
};
</script>
<style lang="scss" scoped>
  .we-filter-view {
    width: 160px;
    position: absolute;
    bottom: 60px;
    top: 40px;
    padding-top: 10px;
    background: #fff;
    border: 1px solid #dcdee2;
    border-left: none;
    padding: 10px;
    z-index: 2;
    opacity: 0.9;
  }
</style>

