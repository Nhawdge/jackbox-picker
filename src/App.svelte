<script>
  import partyPacks from "./data/partypacks.json";

  let players = 4;
  let filteredGames = [];
  let ownedGames = [];

  function filterGames() {
    filteredGames = partyPacks
      .filter((x) => (ownedGames.length ? ownedGames.includes(x.name) : true))
      .map((x) => x.games)
      .flat()
      .filter(
        (game) => players >= game.minplayers && players <= game.maxplayers
      );
    return filteredGames;
  }
  filteredGames = filterGames();
</script>

<main>
  <h1>Jackbox Game Picker</h1>
  Games You own:

  <div class="packs">
    {#each partyPacks as pack, i}
      <button
        on:click={() => {
          if (ownedGames.includes(pack.name)) {
            let gameIndex = ownedGames.findIndex((x) => x == pack.name);
            ownedGames.splice(gameIndex, 1);
            ownedGames = ownedGames;
          } else {
            ownedGames = [...ownedGames, pack.name];
          }
          filteredGames = filterGames();
        }}
        class={`${ownedGames.includes(pack.name) ? "owned" : ""}`}
        >{pack.name}</button
      >
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
        <span> {game.minplayers}-{game.maxplayers}</span>
        <p>{game.description}</p>
      </div>
    {/each}
  </div>
</main>

<style>
  button {
    color: white;
    background-color: rgba(75, 75, 0, 0.25);
  }
  button.owned {
    background-color: rgba(75, 75, 0, 1);
  }
  .game {
    border: solid black 1px;
    margin: 4px;
    border-radius: 3px;
  }
  .games {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
  }
  .packs {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
  }
  main {
    text-align: center;
    padding: 1em;
    max-width: 100%;
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
