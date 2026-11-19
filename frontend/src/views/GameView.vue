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
            <div class="scratch-ticket">
              <img class="ticket-art ticket-size" :src="selected.image" :alt="'Ticket à gratter ' + selected.name">
              <div class="revealed-prize" :class="{ 'is-visible': isRevealed }">
                <strong>{{ selected.gain }}</strong>
                <span>POINTS</span>
              </div>
              <img
                class="ticket-art ticket-cover"
                :class="{ 'is-scratching': isScratching, 'is-scratched': isRevealed }"
                :src="selected.image"
                :alt="'Couverture à gratter du ticket ' + selected.name"
                @animationend="finishScratch"
              >
            </div>
          </div>
          <p class="ticket-caption">{{ isRevealed ? selected.message : 'Clique sur un ticket pour le gratter.' }}</p>
        </section>

        <aside class="game-panel game-picker">
          <h2 class="panel-heading">🎟 Choisir un jeu</h2>
          <section v-for="group in ticketGroups" :key="group.price" class="ticket-level">
            <h3>{{ group.price }} <span>{{ group.label }}</span></h3>
            <a
              v-for="ticket in group.tickets"
              :key="ticket.name"
              href="#"
              :class="{ 'is-selected': selected.name === ticket.name }"
              @click.prevent="selectTicket(ticket)"
            >
              <img class="mini-ticket-art" :src="ticket.image" :alt="'Aperçu ' + ticket.name">
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
import { computed, ref, nextTick } from 'vue';
import ticket1 from '../assets/tickets/ticket_1.JPG';
import ticket2 from '../assets/tickets/ticket_2.JPG';
import ticket3 from '../assets/tickets/ticket_3.JPG';

const tickets = [
  { name: 'Étoile Filante', price: '5 €', group: '5 €', image: ticket1, gain: '10', description: 'À vous la chance', message: 'Bravo ! Vous gagnez 10 points fictifs.' },
  { name: 'Diamant Rouge', price: '10 €', group: '10 €', image: ticket2, gain: '25', description: 'Un éclat de fortune', message: 'Magnifique ! Vous gagnez 25 points fictifs.' },
  { name: 'Royale', price: '20 €', group: '20 €', image: ticket3, gain: '50', description: 'Le grand format', message: 'Royal ! Vous gagnez 50 points fictifs.' }
];

const selected = ref(tickets[2]);
const isScratching = ref(false);
const isRevealed = ref(false);
async function selectTicket(ticket) {
  selected.value = ticket;
  isScratching.value = false;
  isRevealed.value = false;
  await nextTick();
  isScratching.value = true;
}

function finishScratch() {
  if (!isScratching.value) return;
  isScratching.value = false;
  isRevealed.value = true;
}

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

<style scoped>
.scratch-ticket {
  position: relative;
  width: min(100%, 330px);
  overflow: hidden;
  border-radius: 13px;
  background: #f5e8ce;
  box-shadow: 0 13px 22px rgba(75, 45, 32, .24);
}
.ticket-art {
  display: block;
  width: 100%;
  height: auto;
}
.ticket-size {
  visibility: hidden;
}
.ticket-cover {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: fill;
  transform-origin: left center;
}
.ticket-cover.is-scratching {
  animation: scratch-cover 1.6s cubic-bezier(.45,.05,.7,.35) forwards;
}
.ticket-cover.is-scratched {
  visibility: hidden;
  pointer-events: none;
}
.revealed-prize {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 5px;
  color: #7b2f18;
  background: #fff4d9;
  opacity: 0;
  transition: opacity .15s ease;
}
.revealed-prize strong {
  font-size: clamp(3rem, 10vw, 5rem);
  line-height: 1;
  font-weight: 900;
}
.revealed-prize span {
  font-size: .8rem;
  font-weight: 900;
  letter-spacing: .18em;
}
.revealed-prize.is-visible {
  opacity: 1;
}
.mini-ticket-art {
  display: block;
  width: 58px;
  height: 42px;
  flex: 0 0 58px;
  object-fit: cover;
  border-radius: 5px;
}
@keyframes scratch-cover {
  0% {
    clip-path: polygon(0 0, 100% 0, 100% 100%, 0 100%);
    opacity: 1;
  }
  18% {
    clip-path: polygon(0 0, 82% 0, 91% 16%, 78% 31%, 90% 48%, 76% 65%, 86% 82%, 72% 100%, 0 100%);
  }
  40% {
    clip-path: polygon(0 0, 59% 0, 68% 15%, 55% 32%, 70% 49%, 53% 66%, 66% 84%, 50% 100%, 0 100%);
  }
  65% {
    clip-path: polygon(0 0, 31% 0, 40% 16%, 26% 33%, 42% 50%, 25% 67%, 37% 85%, 20% 100%, 0 100%);
  }
  100% {
    clip-path: polygon(0 0, 0 0, 5% 17%, 0 33%, 6% 50%, 0 67%, 5% 84%, 0 100%);
    opacity: 0;
  }
}
.ticket-level a.is-selected { border-color: var(--caramel); background: var(--cream-deep); }
@media (prefers-reduced-motion: reduce) {
  .ticket-cover.is-scratching { animation-duration: .01ms; }
}
</style>
