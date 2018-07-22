<template>
  <div class="rum-data-grid__container" :class="classes.container || []" rum-style-extension>
    <table class="rum-data-grid__grid" :class="classes.grid || []" rum-style-extension>
      <thead v-if="headerVisible">
        <tr class="rum-data-grid__header" :class="classes.header || []" rum-style-extension>
          <th v-for="column in columns"
          :key="column.name"
          class="rum-data-grid__header-cell"
           :class="classes.headerCell || []"
          rum-style-extension>
            <slot :name="`${column.name}-header`" :title="column.title" :items="items">
              <span v-text="column.title"></span>
            </slot>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, itemIndex) in items"
        :key="item[key]"
        class="rum-data-grid__row"
        :class="classes.row || []"
        rum-style-extension>
          <td v-for="column in columns"
          :key="column.name"
          class="rum-data-grid__cell"
          :class="classes.cell || []"
          rum-style-extension>
            <slot :name="column.name"
            :item="item"
            :itemIndex="itemIndex"
            :data="cellData({ item, column, itemIndex, items })">
              <span v-text="cellData({ item, column, itemIndex, items })"></span>
            </slot>
          </td>        
        </tr>
      </tbody>
    </table>
  </div>    
</template>

<script>
export default {
  name: "RumDataGrid",

  props: {
    cols: Array, // { name (unique), title, order, dataSelector }
    headerVisible: Boolean,
    items: Array,
    classes: { type: Object, default: () => ({}) },
    itemKey: String
  },

  data() {
    return {
      userCols: this.cols
    };
  },

  computed: {
    key() {
      return this.itemKey || "id";
    },

    columns() {
      if (this.userCols) {
        return this.userCols.map(c => c).sort((a, b) => a.order > b.order);
      }

      if (!this.items || this.items.length === 0) {
        return [];
      }

      return Object.keys(this.items[0])
        .filter(name => name !== this.key)
        .map((name, i) => ({ name, title: name, order: i }));
    },

    cellData() {
      return ({ item, column, itemIndex, items }) =>
        column.dataSelector
          ? column.dataSelector({ item, itemIndex, items })
          : item[column.name];
    }
  }
};
</script>

<style lang="scss" src="./RumDataGrid.scss" scoped></style>