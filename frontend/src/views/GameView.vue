<template>
  <div class="game-page scratch-page">
    <div class="game-shell">
      <div class="game-layout">
        <aside class="game-panel leaderboard">
          <h2 class="panel-heading">🏆 Classement</h2>
          <p class="leaderboard-kicker">Les plus chanceux du club</p>
          <div v-for="player in leaderboard" :key="player.name" class="leader-row" :class="{ 'player-row': player.current }">
            <span class="leader-rank">{{ player.rank }}</span><strong>{{ player.name }}</strong><span class="leader-score">{{ player.score }}</span>
          </div>
        </aside>
        <section class="game-center scratch-center">
          <p class="game-eyebrow">Ticket à la une</p>
          <h2 class="game-title">{{ selected.name }}</h2>
          <div class="scratch-stage">
          </div>
          <p class="ticket-caption">Sélectionne un ticket dans la liste à droite.</p>
        </section>
        <aside class="game-panel game-picker">
          <h2 class="panel-heading">🎟 Choisir un jeu</h2>
          <section v-for="group in ticketGroups" :key="group.price" class="ticket-level">
            <h3>{{ group.price }} <span>{{ group.label }}</span></h3>
            <a v-for="ticket in group.tickets" :key="ticket.name" href="#" @click.prevent="selected = ticket">
              <span class="mini-ticket" :class="ticket.color">{{ ticket.symbol }}</span>
              <span><strong>{{ ticket.name }}</strong><small>{{ ticket.description }}</small></span>
            </a>
          </section>
        </aside>
      </div>
      <p class="sample-note">Maquette visuelle uniquement — tickets, scores et gains fictifs. Aucun jeu ni paiement n'est activé.</p>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import { RouterLink } from 'vue-router';

const tickets = [
  { name: 'Étoile Filante', title: 'ÉTOILE\nFILANTE', price: '5 €', group: '5 €', symbol: '✦', color: 'mini-ticket-blue', description: 'À vous la chance' },
  { name: 'Diamant Rouge', title: 'DIAMANT\nROUGE', price: '10 €', group: '10 €', symbol: '♦', color: 'mini-ticket-red', description: 'Un éclat de fortune' },
  { name: 'Royale', title: 'ROYALE', price: '20 €', group: '20 €', symbol: '♛', color: 'mini-ticket-purple', description: 'Le grand format' }
];


const selected = ref(tickets[2]);
const ticketGroups = computed(() => [
  { price: '5 €', label: 'Débutant', tickets: tickets.filter(t => t.group === '5 €') },
  { price: '10 €', label: 'Classique', tickets: tickets.filter(t => t.group === '10 €') },
  { price: '20 €', label: 'Chaperonné', tickets: tickets.filter(t => t.group === '20 €') }
]);


const leaderboard = [
  { rank: '🥇', name: 'LuckyStar', score: '12 840 pts' },
  { rank: '🥈', name: 'GoldenTouch', score: '9 620 pts' },
  { rank: '🥉', name: 'TrèfleVert', score: '7 450 pts' },
  { rank: '4', name: 'Chanceux', score: '5 180 pts' },
  { rank: '5', name: 'ÉtoileFilante', score: '3 960 pts' },
  { rank: '6', name: 'Vous', score: '2 450 pts', current: true }
];
</script>
