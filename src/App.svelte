<script>
  import {} from "os";
  import partyPacks from "./data/partypacks.json";

  let players = 4;
  let filteredGames = [];

  function filterGames() {
    filteredGames = partyPacks
      .map((x) => x.games)
      .flat()
      .filter((game) => {
        return players >= game.minplayers && players <= game.maxplayers;
      });
    return filteredGames;
  }
  filteredGames = filterGames();
</script>

<main>
  <h1>Jackbox Game Picker</h1>
  Games You own:

  <div class="packs">
    {#each partyPacks as pack, i}
      <button>{pack.name}</button>
    {/each}
  </div>
  <div class="filters">
    Players
    <input
      type="range"
      bind:value={players}
      min="2"
      step="1"
      max="10"
      on:change={() => (filteredGames = filterGames())}
    />
    {players}
  </div>
  <div class="games">
    {#each filteredGames as game}
      <div class="game">
        <img
          src={game.image || `//placehold.it/250?text=${game.name}`}
          alt=""
        />
        <h3>
          {game.name}
        </h3>
        <p>{game.description}</p>
      </div>
    {/each}
  </div>
</main>

<style>
  .game {
    border: solid black 1px;
    margin: 2px;
    border-radius: 3px;
    padding: 4px;
  }
  .games {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
  }
  .packs {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
  }
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
