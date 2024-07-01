<script>
  import { Radio } from "flowbite-svelte";
  import { Button } from "flowbite-svelte";

  let api = [
    "https://opentdb.com/api.php?amount=10&category=18&difficulty=medium&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18&difficulty=medium&type=multiple",
    "https://opentdb.com/api.php?amount=10&category=18&difficulty=hard&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18&difficulty=hard&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18",
  ];
  let allQuestions = [];
  let totalQuestions = 0;
  let userSelections = [];
  let score = null;
  let correctAnswers = 0;
  let quizGenerated = false;

  function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  }

  async function fetchQuestions(index) {
    let response = await fetch(api[index]);
    let data = await response.json();
    allQuestions = data.results.map((q, i) => {
      let options = shuffleArray([...q.incorrect_answers, q.correct_answer]);
      return { ...q, options, index: i };
    });
    userSelections = allQuestions.map(() => null);
    score = null;
    correctAnswers = 0;
    totalQuestions = allQuestions.length;
    quizGenerated = true;
  }

  function handleSelection(questionIndex, selectedOption) {
    userSelections[questionIndex] = selectedOption;
  }

  function submit() {
    for (let i = 0; i < userSelections.length; i++) {
      if (userSelections[i] === allQuestions[i].correct_answer) {
        correctAnswers++;
      }
    }
    score = `You got ${correctAnswers} out of ${allQuestions.length} correct!`;
    allQuestions = [];
    userSelections = [];
    quizGenerated = false;
  }
</script>

<main>
  <div class="generate-btns">
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(0)}>Short Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(1)}>Big Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(2)}>Hard and Short Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(3)}>hard and Big Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(4)}>Random Quiz</Button
    >
  </div>
  {#each allQuestions as question, i}
    <div class="radio-group">
      <h3>Question {i + 1}:</h3>
      <p>{@html question.question}</p>
      {#each question.options as option}
        <Radio
          name={`option${i}`}
          value={option}
          class="options"
          on:change={() => handleSelection(i, option)}
        >
          {@html option}
        </Radio>
      {/each}
      <hr />
    </div>
  {/each}
  {#if quizGenerated}
    <div id="submit-btn">
      <Button on:click={submit}>Submit</Button>
    </div>
  {/if}
  {#if score}
    {#if correctAnswers < totalQuestions / 4}
      <p class="submit-para" style="color: red;">{score}</p>
    {:else}
      <p class="submit-para">{score}</p>
    {/if}
  {/if}
  <br />
  <br />
  <br />
  <br />
</main>

<style>
  main {
    padding-top: 5vh;
  }
  h3 {
    font-size: 2vw;
  }
  p {
    font-size: 1.5vw;
  }
  .radio-group {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 2vw;
  }
  :global(.radio-group label) {
    color: antiquewhite;
    font-size: 1vw;
  }
  .radio-group {
    margin-left: 3vw;
  }
  hr {
    margin-right: 2vw;
  }
  .generate-btns {
    display: flex;
    gap: 2vw;
    justify-content: center;
    margin-bottom: 10vh;
  }
  :global(#submit-btn) {
    display: flex;
    margin-top: 2vw;
    justify-content: center;
  }
  .submit-para {
    color: green;
    font-size: 3vw;
    text-align: center;
    margin-top: 20vh;
  }
</style>
