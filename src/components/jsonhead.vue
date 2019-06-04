<template>
  <div class="json_header">
    <h1>JSONインポート</h1>
    <input @change="fileChange" type="file" id="file_input_expense" name="file_input_expense" accept="application/json">
  </div>
</template>
<script>
export default {
  methods: {
    fileChange: function(e) {
      const file = e.target.files[0];
      const reader = new FileReader();
      const workers = [];
      const loadFunc = () => {
        this.$emit('child-event', reader.result);
      };
      reader.onload = loadFunc;
      reader.readAsBinaryString(file);
    }
  }
};
</script>
<style>
  .json_header {
    float:left;
    width:30vh;
    margin: 0px 20px 20px 0px;
    padding: 10px;
    background: beige;
  }
</style>
