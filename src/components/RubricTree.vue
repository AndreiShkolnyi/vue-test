<template>
  <div class="flex-col gap-2">
    <div>
      <h3 class="font-bold text-2xl">Total Selected Count: {{ totalSelectedCount }}</h3>
    </div>
    <div class="form-check flex gap-2">
      <input type="checkbox" class="form-check-input" v-model="showEmpty" @change="fetchRubrics">
      <label class="form-check-label font-bold text-sm">Отображать пустые рубрики</label>
    </div>



    <tree-node v-for="rootNode in tree" :key="rootNode.id"
               :node="rootNode" @update-total="updateTotal" ></tree-node>
  </div>
</template>

<script>
import TreeNode from './TreeNode.vue';

export default {
  components: {
    'tree-node': TreeNode
  },
  data() {
    return {
      tree: [],
      showEmpty: false,
      totalSelectedCount: 0
    };
  },
  created() {
    this.fetchRubrics()
  },
  methods: {
    fetchRubrics() {
      const apiUrl = `https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${this.showEmpty ? 1 : 0}`;

      fetch(apiUrl, {
        mode: 'no-cors'
      })
          .then(response => response.json())
          .then(data => {
            this.tree = data;
            this.totalSelectedCount = 0;
          });
    },
    updateTotal({ count }) {
      this.totalSelectedCount += count;
    }
  }
};
</script>
