<script>
  import Navbar from "./navbar.svelte";
  import Questions from "./questions.svelte";
</script>

<main>
  <Navbar></Navbar>
  <p>Click a button to get a new quiz!!</p>
  <Questions></Questions>
</main>

<style>
  p {
    padding-top: 15vh;
    text-align: center;
    font-size: 1.5vw;
  }
</style>
