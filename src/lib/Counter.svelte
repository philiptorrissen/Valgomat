<script>
  let count = 0
  const increment = () => {
    count += Math.random(3)
    console.log(count)
  }
</script>

<button on:click={increment}>
  Antall klikk {count}
  
</button>
