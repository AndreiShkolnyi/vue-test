<template>
  <div class="mb-4 mt-2 bg-gray-200 rounded-lg p-3 max-w-[370px] shadow-xl">
    <div class="flex gap-2">
      <input type="checkbox" v-model="isSelected" @change="toggleSelection">
      <a :href="'https://www.klerk.ru' + node.url" target="_blank">{{ node.title }}</a>
      ({{ node.count }} - {{ totalCount }})
      <span v-if="hasChildren" @click="toggleNode" style="cursor: pointer;">
        {{ isOpen ? '[↑]' : '[↓]' }}
      </span>
    </div>
    <div v-if="isOpen && hasChildren">
      <tree-node v-for="child in node.children" :key="child.id"
                 :node="child" @update-total="updateTotal"></tree-node>
    </div>
  </div>
</template>

<script>
export default {
  props: ['node'],
  data() {
    return {
      isOpen: false,
      isSelected: false
    };
  },
  computed: {
    totalCount() {
      const count = this.node.count || 0;
      const childrenCount = this.node.children ? this.node.children.reduce((sum, child) => sum + (child.count || 0), 0) : 0;
      return count + childrenCount;
    },
    hasChildren() {
      return this.node.children && this.node.children.length > 0;
    }
  },
  methods: {
    toggleNode() {
      this.isOpen = !this.isOpen;
    },
    toggleSelection() {
      this.$emit('update-total', { count: this.isSelected ? this.totalCount : -this.totalCount });
    },
    updateTotal({ count }) {
      this.$emit('update-total', { count });
    }
  }
};
</script>
