<template>
  <div class="mb-4 mt-2 bg-gray-200 rounded-lg p-3 max-w-[370px] shadow-xl">
    <div class="flex gap-2">
      <input type="checkbox"
             :disabled="isDisabled"
             v-model="isSelected"
             @change="toggleSelection(node, 'parent')">
      <a
          :href="'https://www.klerk.ru' + node.url"
          target="_blank">
        {{ node.title }}
      </a>
      ({{ node.count }} - {{ totalCount }})
      <span
          v-if="hasChildren"
          @click="toggleNode"
          style="cursor: pointer;">
        {{ isOpen ? '[↑]' : '[↓]' }}
      </span>
    </div>
    <div v-if="isOpen && hasChildren">
      <tree-node
          v-for="child in node.children"
          :key="child.id"
          :parent-selected="isSelected"
          :node="child"
          @update-total="toggleSelection(child, 'child')">

      </tree-node>
    </div>
  </div>
</template>

<script>
export default {
  props: ['node', 'parentSelected', 'childSelected'],
  data() {
    return {
      isOpen: false,
      isSelected: false,
      childrenCount: [],
    };
  },
  computed: {
    totalCount() {
      const count = this.node.count || 0;
      const childrenCount = this.node.children ? this.node.children
          .reduce((sum, child) => sum + (child.count || 0), 0) : 0;
      return count + childrenCount;
    },
    hasChildren() {
      return this.node.children && this.node.children.length > 0;
    },
    isDisabled() {
      return this.parentSelected;
    },
  },
  watch: {
    parentSelected: function (newVal) {
      if (newVal) {
        this.isSelected = false;
      }
    }
  },
  methods: {
    cleanTotal() {
      if(this.hasChildren) {
        this.node.children.forEach(item => {
          if (this.childrenCount.includes(item.id)) {
            this.$emit('update-total', { count: -item.count})
          }
        });
        this.childrenCount = [];
      }
    },
    toggleNode() {
      this.isOpen = !this.isOpen;
    },
    toggleSelection(node, type) {
      if(type === 'parent') {
        this.cleanTotal();
        this.$emit('update-total', { count: this.isSelected ? this.totalCount : -this.totalCount });
      } else if (type === 'child') {
        if(this.childrenCount.includes(node.id)) {
          this.$emit('update-total', {count: -node.count});
          this.childrenCount = this.childrenCount.filter(item => node.id !== item);
        } else {
          this.$emit('update-total', {count: node.count});
          this.childrenCount.push(node.id);
        }
      }
    },
  }
};
</script>
