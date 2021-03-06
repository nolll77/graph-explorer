<template>
  <table class="app-table">
    <thead>
      <tr>
        <th
          v-for="(key, index) in columns"
          :key="index"
          :class="{ active: sortKey == key }"
          @click="sortBy(key)"
        >
          {{ key | format }}
          <span
            :class="sortOrders[key] > 0 ? 'asc' : 'dsc'"
            class="arrow"
          />
        </th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(row, index) in sortedRows"
        :key="index">
        <td
          v-for="(cell, index) in row"
          :key="index">
          {{ cell }}
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>

export default {
  filters: {
    format(str) {
      return str.split('_').join(' ');
    }
  },
  props: {
    columns: {
      type: Array,
      default: () => []
    },
    rows: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      sortOrders: {},
      sortKey: ''
    };
  },
  computed: {
    sortedRows() {
      /* eslint no-param-reassign: ["error", { "props": true }] */
      let { rows } = this;
      const { sortKey } = this;
      const order = this.sortOrders[sortKey] || 1;
      if (sortKey) {
        const sortKeyIdx = this.columns.indexOf(sortKey);
        rows = rows.slice().sort((a, b) => {
          a = a[sortKeyIdx];
          b = b[sortKeyIdx];
          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }
      return rows;
    }
  },
  watch: {
    columns() {
      const vm = this;
      this.columns.forEach((key) => {
        vm.$set(vm.sortOrders, key, 1);
      });
    }
  },
  methods: {
    sortBy(key) {
      this.sortKey = key;
      this.sortOrders[key] = this.sortOrders[key] * -1;
    }
  }
};
</script>


<style scoped lang="scss">
  @import "../scss/settings.scss";
  table {
    border: 2px solid $turquoise;
    border-radius: 3px;
    background-color: #fff;
  }
  th {
    background-color: $turquoise;
    color: rgba(255,255,255,0.66);
    cursor: pointer;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }
  td {
    background-color: #f9f9f9;
  }
  th, td {
    min-width: 120px;
    padding: 10px 20px;
    white-space: nowrap;
  }
  th.active {
    color: #fff;
  }
  th.active .arrow {
    opacity: 1;
  }
  .arrow {
    display: inline-block;
    vertical-align: middle;
    width: 0;
    height: 0;
    margin-left: 5px;
    opacity: 0.66;
  }
  .arrow.asc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-bottom: 4px solid #fff;
  }
  .arrow.dsc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-top: 4px solid #fff;
  }
</style>
