<script>
  import partyPacks from "./data/partypacks.json";

  let players = 4;
  let filteredGames = [];
  let ownedGames = [];
  let validFilters = [];
  let tagFilters = [];

  function filterGames() {
    partyPacks.forEach((pack) => {
      pack.games.forEach((game) => {
        console.log('g', game)
        game['packName']=pack.name
        game['steamStore']=pack.steamStore
        game['steamAppId']=pack.steamAppId
      })
    })
    console.log("flattened", partyPacks)
    filteredGames = partyPacks
      .filter((x) => (ownedGames.length ? ownedGames.includes(x.name) : true))
      .map((x) => x.games)
      .flat()
      .filter((game) =>
        tagFilters.length
          ? game.gameType.filter((x) => tagFilters.includes(x)).length
          : true
      )
      .filter(
        (game) => players >= game.minplayers && players <= game.maxplayers
      );
    return filteredGames;
  }
  function getFilters(filteredGames) {
    validFilters = Array.from(
      new Set(filteredGames.map((x) => x.gameType).flat())
    );
    return validFilters;
  }
  filteredGames = filterGames();
  validFilters = getFilters(filteredGames);

  $: tagFilters, (filteredGames = filterGames());
</script>

<main>
  <h1>Jackbox Game Picker</h1>
  Games you own:
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
    <div class="tags">
      {#each validFilters as filter}
        <div>
          <input
            type="checkbox"
            value={filter}
            bind:group={tagFilters}
            id={filter}
          />
          <label for={filter}> {filter} </label>
        </div>
      {/each}
    </div>
  </div>
  <div class="games">
    {#each filteredGames as game}
      <div class="game">
        <img
          src={game.image || `//placehold.it/350x150?text=${game.name}`}
          alt=""
        />
        <h3>
          {game.name}
        </h3>
        <span> {game.minplayers}-{game.maxplayers}</span>
        <span> {game.gameType} </span>
        <p><small><a href={game.steamStore} target="_blank" rel="noopener noreferer">{game.packName}</a> (<a href={`steam://run/${game.steamAppId}`}>Run</a>)</small></p>
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
    img {
      background-color:lightgray;
    }
  }

  main {
    text-align: center;
    padding: 1em;
    max-width: 1000px;
    margin: 0 auto;
  }
  img {
    max-width: 100%;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  .packs {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
  }

  .games {
    display: grid;
    grid-template-columns: 1fr;
  }
  @media screen and (min-width: 750px) {
    .packs {
      grid-template-columns: repeat(7, 1fr);
    }
    .games {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  .tags {
    display: flex;
    place-content: space-evenly;
  }
  .tags input {
    display: none;
  }

  .tags label {
    background-color: hsla(10, 50%, 50%);
    min-width: 100px;
    padding: 10px;
    border-radius: 5px;
  }
  input:checked + label {
    background-color: hsla(110, 50%, 50%);
  }
</style>
