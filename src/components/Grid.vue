<template>
  <div class="grid-container">
    <div
      class="row"
      v-for="index in rows"
      :key="index"
      :style="{
				width: `${gridItemSize * columns}px`,
				height: `${gridItemSize}px`
			}"
    >
      <div
        class="column"
        v-for="index in columns"
        :key="index"
        :style="{
					width: `${gridItemSize}px`,
					height: `${gridItemSize}px`
				}"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["columns", "rows", "gridItemSize"],
  data() {
    return {};
  }
};
</script>

<style lang="scss">
.grid-container {
  margin: 0px auto;
  position: relative;
  width: auto;
  border: 1px solid #1b998b;
  box-sizing: border-box;
}

.row {
  margin: 0;
  padding: 0;
}

.column {
  border: 1px solid #1b998b;
  display: inline-block;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>