<script>
  import { Radio, Label, Input, Select, Button } from "flowbite-svelte";

  let categories = [
    { value: "0", name: "Any Category" },
    { value: "9", name: "General Knownledge" },
    { value: "15", name: "Entertainment: Video Games" },
    { value: "16", name: "Entertainment: Board Games" },
    { value: "29", name: "Entertainment: Comics" },
    { value: "17", name: "Science & Nature" },
    { value: "18", name: "Science: Computers" },
    { value: "21", name: "Sports" },
    { value: "28", name: "Vehicles" },
  ];
  let difficulties = [
    { value: "0", name: "Any Difficulty" },
    { value: "easy", name: "Easy" },
    { value: "medium", name: "Medium" },
    { value: "hard", name: "Hard" },
  ];
  let types = [
    { value: "0", name: "Any Type" },
    { value: "multiple", name: "Multiple Choice" },
    { value: "boolean", name: "True / False" },
  ];
  let noOfQuestions = 1;
  let category = "0";
  let difficulty = "0";
  let type = "0";
  let customApiBase = "https://opentdb.com/api.php?amount=";
  let customButtonClicked = false;
  let allQuestions = [];
  let totalQuestions = 0;
  let userSelections = [];
  let score = null;
  let correctAnswers = 0;
  let quizGenerated = false;

  let api = [
    "https://opentdb.com/api.php?amount=10&category=18&difficulty=medium&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18&difficulty=medium&type=multiple",
    "https://opentdb.com/api.php?amount=10&category=18&difficulty=hard&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18&difficulty=hard&type=multiple",
    "https://opentdb.com/api.php?amount=20&category=18",
  ];

  async function fetchQuestions(url) {
    let response = await fetch(url);
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

  async function getRandomQuiz() {
    let customApi = `${customApiBase}${noOfQuestions}`;
    if (category !== "0") {
      customApi += `&category=${category}`;
    }
    if (difficulty !== "0") {
      customApi += `&difficulty=${difficulty}`;
    }
    if (type !== "0") {
      customApi += `&type=${type}`;
    }
    await fetchQuestions(customApi);
    customButtonClicked = false;
  }

  function custombtnhandler() {
    resetQuizState();
    customButtonClicked = true;
  }

  function resetQuizState() {
    allQuestions = [];
    totalQuestions = 0;
    userSelections = [];
    score = null;
    correctAnswers = 0;
    quizGenerated = false;
  }

  function getRandomSumbit() {
    customButtonClicked = false;
    getRandomQuiz();
  }

  function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
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
  <p id="getquiz-para">Click a button to get a new quiz!!</p>
  <div class="generate-btns">
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(api[0])}>Short Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(api[1])}>Big Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(api[2])}>Hard and Short Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(api[3])}>Hard and Big Quiz</Button
    >
    <Button
      color="green"
      class="generate-btn"
      on:click={() => fetchQuestions(api[4])}>Random Quiz</Button
    >
    <Button color="green" class="generate-btn" on:click={custombtnhandler}
      >Custom Quiz</Button
    >
  </div>

  {#if customButtonClicked}
    <section class="section">
      <div class="selectionouter">
        <div class="selectioninner">
          <h3 class="h3-tag">Custom Trivia</h3>
          <Label for="noofquestions" class="mt-2 lables"
            >Enter no of questions</Label
          >
          <Input
            id="noofquestions"
            type="number"
            bind:value={noOfQuestions}
            placeholder="no of questions"
            min="1"
          ></Input>
          <Label class="lables"
            >Select an option
            <Select class="mt-2" items={categories} bind:value={category} />
          </Label>
          <Label class="lables"
            >Select an option
            <Select class="mt-2" items={difficulties} bind:value={difficulty} />
          </Label>
          <Label class="lables"
            >Select an option
            <Select class="mt-2" items={types} bind:value={type} />
          </Label>
          <Button
            outline
            color="purple"
            id="getrandom"
            on:click={getRandomSumbit}>Get Quiz</Button
          >
        </div>
      </div>
    </section>
  {:else if quizGenerated}
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

  <br /><br /><br /><br />
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
  #getquiz-para {
    font-family: "Playwrite DE Grund", cursive;
    padding-top: 10vh;
    margin-bottom: 3vh;
    text-align: center;
    font-size: 1.9rem;
  }
  hr {
    margin-right: 2vw;
  }
  .generate-btns {
    display: flex;
    gap: 2vw;
    justify-content: center;
  }
  :global(#submit-btn) {
    display: flex;
    margin-top: 2vw;
    justify-content: center;
  }
  .submit-para {
    color: green;
    font-size: 2rem;
    text-align: center;
    margin-top: 20vh;
  }
  .h3-tag {
    font-size: 1.35rem;
  }
  .section {
    margin-top: 3vh;
  }
  .selectioninner {
    width: 30vw;
    display: flex;
    flex-direction: column;
    gap: 1.2vh;
    justify-content: space-between;
  }
  .selectionouter {
    display: flex;
    justify-content: center;
  }
  :global(.lables) {
    color: blueviolet;
    font-size: 1rem;
  }
</style>
