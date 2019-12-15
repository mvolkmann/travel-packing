<script>
  export let percent;
</script>

<div class="bar">
  <div class="progress" style={`width: ${percent}%`} />
</div>

<style>
  .bar {
    background-color: white;
    border: solid lightgray 1px;
    height: 10px;
    width: 100%;
  }

  .progress {
    background-color: orange;
    height: 100%;
  }
</style>
