<template>
  <div class="_header">
    <h1>CSVインポート</h1>
    <input @change="fileChange" type="file" id="file_input_expense" name="file_input_expense" accept="text/csv">
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
        const lines = reader.result.split("\n");
        lines.forEach(element => {
          const workerData = element.split(",");
          if (workerData.length != 5) return;
          const worker = {
            title: workerData[0],
            lat: workerData[1],
            lng: workerData[2],
            iconColor:workerData[3],
            contents:workerData[4]
          };
          workers.push(worker);
        });
        this.$emit('child-event', JSON.stringify(workers));
      };
      reader.onload = loadFunc;
      reader.readAsBinaryString(file);
    }
  }
};
</script>
