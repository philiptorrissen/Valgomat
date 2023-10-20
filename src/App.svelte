<!-- Valgomat.svelte -->
<script>
  // Leser spørsmål fra JSON fil
  import questions from './assets/questions.json';

  // Lager en array for brukerens svar på hvert spørsmål
  let userAnswers = [];
  let partyDistance = new Map();
  let currentQuestionIndex = 0;

  // En funksjon for å beregne poengsummene basert på hvor enig brukeren
  // er med hvert parti på hvert spørsmål
  function calculateScores() {
    let question = questions[currentQuestionIndex];

    question.opinions.forEach((opinion) => {
      addOrUpdatePartyScore(
        partyDistance,
        opinion.party,
        Math.abs(opinion.score - userAnswers[currentQuestionIndex])
      );
    });

    if (currentQuestionIndex < questions.length - 1) {
      currentQuestionIndex++;
    } else {
      // Alle spørsmål er besvart, vis resultater eller gjør noe annet du ønsker.
    }
  }

  // Denne funksjoner oppdaterer map med avstand til partiet. Hvis
  // partiet ikke finnes i mapet
  // legger vi det til
  function addOrUpdatePartyScore(map, party, value) {
    if (map.get(party)) {
      map.set(party, map.get(party) + value);
    } else {
      map.set(party, value);
    }
  }

  // Function to determine the party with the lowest distance
  function findMostAgreeableParty() {
    // Create an array to hold the parties and their distances
    const partyDistances = [...partyDistance.keys()].map((party) => ({
      party,
      distance: partyDistance.get(party) || 0, // Use 0 if the party doesn't have a distance
    }));

    // Sort the parties by distance in ascending order
    partyDistances.sort((a, b) => a.distance - b.distance);

    // Get the party with the lowest distance (most agreement)
    return partyDistances[0];
  }

  // Function to display the most agreeable party
  function displayMostAgreeableParty() {
    const mostAgreeableParty = findMostAgreeableParty();
    if (mostAgreeableParty) {
      return `Du er mest enig med partiet: ${mostAgreeableParty.party} (${mostAgreeableParty.distance} poeng)`;
    }
    return "Ingen partier funnet";
  }
</script>

<main>
  <div>
    <h1>Valgomat</h1>
    {#if currentQuestionIndex < questions.length - 1}
      <div class="radiobutton">
        <p>{questions[currentQuestionIndex].text}</p>
        {#each questions[currentQuestionIndex].options as option}
          <label>
            <input
              type="radio"
              value={option.value}
              bind:group={userAnswers[currentQuestionIndex]}
            />
            {option.text}
          </label>
        {/each}
        <button on:click={calculateScores}>Neste</button>
      </div>
    {:else}
      <h2>Resultater:</h2>
      <p>Under følger en rangert liste over de partiene du er mest enig med. Kalkulatoren fungerer slik at den regner akkumulert avstand for alle spørsmålene. Hvis ett parti er helt enig i en påstand og du er litt enig, vil avstanden være 1. Hvis du er litt uenig, vil avstanden være 2. Osv</p>
      <p>{displayMostAgreeableParty()}</p>
      <ul>
        {#each  [...partyDistance.keys()] as party}
        <p>{party} {partyDistance.get(party)}  </p>
        {/each}
      </ul>
    {/if}


  </div>
  <div class="bunntekstdiv">
  <p class="bunn">Laget av Philip Torrissen</p>
  </div>
</main>
<style>
  .bunn{
    margin-top: 50%;
    margin-bottom: 0%;
    color: rgb(173, 173, 173);
  }
  .bunntekstdiv{

    width: 100%;
  }
</style>
