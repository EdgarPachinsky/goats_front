<script>
  import { Line } from "vue-chartjs";

  export default {
    extends: Line,
    props: ["data", "options"],
    mounted() {
      // this._chart.destroy();
      this.renderChart(this.data, this.options);
    },
    watch: {
      data: function() {

        this.$data._chart.update()
        //this.renderChart(this.data, this.options);
        // this.renderChart(this.data, this.options);
      }
    }
  };
</script>
