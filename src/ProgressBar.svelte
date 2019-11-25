<script>
  export let percent;
</script>

<style>
  .bar {
    background-color: white;
    border: solid black 1px;
    height: 10px;
    width: 100%;
  }

  .progress {
    background-color: pink;
    height: 100%;
  }
</style>

<div class="bar">
  <div class="progress" style={`width: ${percent}%`} />
</div>
