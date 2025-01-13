<script lang="ts">
    import '../app.css';
  import { flip } from 'svelte/animate';
  import { fade, fly, scale } from 'svelte/transition';

  let cards = [
    { id: 1, color: '#E066A0', headerColor: '#B54B82' },
    { id: 2, color: '#E67345', headerColor: '#B85835' },
    { id: 3, color: '#D4B400', headerColor: '#A68C00' }
  ];

  let collected: typeof cards = [];
  let isBoxVisible = true;
  let areCardsDealt = false;
  
  async function dealCards() {
    isBoxVisible = false;
    // Small delay to let box disappear
    await new Promise(resolve => setTimeout(resolve, 400));
    areCardsDealt = true;
  }

  function collectCard(card: typeof cards[0]) {
    cards = cards.filter(c => c.id !== card.id);
    collected = [...collected, card];
    
    // Show box again when all cards are collected
    if (cards.length === 0) {
      setTimeout(() => {
        areCardsDealt = false;
        isBoxVisible = true;
      }, 600);
    }
  }
</script>

<div class="game-area">
  <!-- Box -->
  {#if isBoxVisible}
    <div 
      class="box"
      on:click={dealCards}
      transition:scale
    />
  {/if}

  <!-- Cards to collect -->
  {#if areCardsDealt}
    <div class="cards-container">
      {#each cards as card, i (card.id)}
        <div 
          class="card"
          style="background: {card.color}"
          on:click={() => collectCard(card)}
          animate:flip
          transition:fly={{
            y: 200,
            x: -100 + (i * 100),
            duration: 400,
            delay: i * 100
          }}
        >
          <div class="card-header" style="background: {card.headerColor}">Name</div>
        </div>
      {/each}
    </div>
  {/if}

  <!-- Collection area -->
  <div class="collection">
    {#each collected as card (card.id)}
      <div 
        class="card collected"
        style="background: {card.color}"
        animate:flip
        transition:fly={{ x: 100, duration: 400 }}
      >
        <div class="card-header">Name</div>
      </div>
    {/each}
  </div>
</div>

<style>
  .game-area {
    position: relative;
    min-height: 500px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .box {
    width: 120px;
    height: 180px;
    background: #98FB98;
    border-radius: 12px;
    cursor: pointer;
    position: relative;
  }

  .box::before,
  .box::after {
    content: '';
    position: absolute;
    left: 0;
    right: 0;
    height: 20px;
    background: repeating-linear-gradient(
      90deg,
      rgba(0, 0, 0, 0.1),
      rgba(0, 0, 0, 0.1) 2px,
      transparent 2px,
      transparent 4px
    );
  }

  .box::before {
    top: 0;
    border-radius: 12px 12px 0 0;
  }

  .box::after {
    bottom: 0;
    border-radius: 0 0 12px 12px;
  }

  .cards-container {
    position: absolute;
    display: flex;
    gap: 20px;
    justify-content: center;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  
  .card {
    width: 120px;
    height: 180px;
    border-radius: 12px;
    margin: 10px;
    cursor: pointer;
    box-shadow: 0 2px 8px rgba(0,0,0,0.2);
    border: 2px solid white;
  }

  .card-header {
    padding: 8px;
    border-radius: 10px 10px 0 0;
    color: white;
  }

  .collection {
    position: fixed;
    top: 20px;
    right: 20px;
    display: flex;
    gap: 4px;
  }

  .collected {
    transform: scale(0.6);
  }
</style>