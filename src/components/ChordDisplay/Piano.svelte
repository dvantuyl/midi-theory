<script>
  export let keys = [];
</script>

<div id="piano">
  <div
    style="display: inline-block; position: relative; margin: 0px; padding: 0px; border-style: none; user-select: none; cursor: default; height: 151px;"
  >
    {#each Array(35) as _, i}
      <button class="white" style={`left: ${i * 42}px;`} />
    {/each}

    {#each Array(5) as _, i}
      <button class="black" style={`left: ${i * 294 + 27}px;`} />
      <button class="black" style={`left: ${i * 294 + 75}px;`} />
      <button class="black" style={`left: ${i * 294 + 150}px;`} />
      <button class="black" style={`left: ${i * 294 + 198}px;`} />
      <button class="black" style={`left: ${i * 294 + 246}px;`} />
    {/each}
  </div>
</div>

<style>
  #piano .white {
    background-color: white;
    border-color: black;
    display: inline-block;
    position: absolute;
    margin: 0px;
    padding: 0px;
    border-style: solid;
    border-width: 1px;
    width: 41px;
    height: 149px;
    top: 0px;
    left: 0px;
  }

  #piano .black {
    background-color: black;
    border-color: black;
    display: inline-block;
    position: absolute;
    margin: 0px;
    padding: 0px;
    border-style: solid;
    border-width: 1px;
    width: 23px;
    height: 99px;
    top: 0px;
    left: 27px;
  }
</style>
