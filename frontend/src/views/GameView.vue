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
              <div class="ticket-topline"><span>SCRATCH CLUB</span><span>{{ selected.price }}</span></div>
              <div class="ticket-stars">✦ ✧ ✦</div>
              <div class="ticket-title">{{ selected.title }}</div>
              <div class="scratch-result">
                <span v-for="(symbol, index) in selected.result" :key="index">{{ symbol }}</span>
              </div>
              <div class="ticket-bottom"><span>GRATTEZ ET DÉCOUVREZ</span><strong>{{ selected.price }}</strong></div>
              <div
                class="scratch-foil"
                :class="{ 'is-scratching': isScratching, 'is-revealed': isRevealed }"
                :style="{ '--scratch-angle': scratchAngle + 'deg' }"
                @animationend="finishScratch"
              >
                <span class="foil-label">GRATTEZ ICI</span>
                <span class="foil-stars">✦ ✧ ✦ ✧ ✦</span>
              </div>
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
import { computed, ref, nextTick } from 'vue';

const tickets = [
  { name: 'Étoile Filante', title: 'ÉTOILE\nFILANTE', price: '5 €', group: '5 €', symbol: '✦', color: 'mini-ticket-blue', description: 'À vous la chance', result: ['★', '★', '★'], message: 'Bravo ! Trois étoiles : 10 points fictifs.' },
  { name: 'Diamant Rouge', title: 'DIAMANT\nROUGE', price: '10 €', group: '10 €', symbol: '♦', color: 'mini-ticket-red', description: 'Un éclat de fortune', result: ['♦', '♦', '♦'], message: 'Magnifique ! Trois diamants : 25 points fictifs.' },
  { name: 'Royale', title: 'ROYALE', price: '20 €', group: '20 €', symbol: '♛', color: 'mini-ticket-purple', description: 'Le grand format', result: ['♛', '♛', '♛'], message: 'Royal ! Trois couronnes : 50 points fictifs.' }
];

const selected = ref(tickets[2]);
const isScratching = ref(false);
const isRevealed = ref(false);
const scratchAngle = ref(0);

async function selectTicket(ticket) {
  selected.value = ticket;
  // Chaque grattage part dans une direction différente.
  scratchAngle.value = [0, 90, 180, 270][Math.floor(Math.random() * 4)] + (Math.random() * 24 - 12);
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
  display: flex;
  width: min(100%, 330px);
  min-height: 220px;
  flex-direction: column;
  justify-content: space-between;
  overflow: hidden;
  padding: 17px 19px;
  border: 5px double #f7d98e;
  border-radius: 13px;
  background: linear-gradient(140deg, #9b552b, #d99a52 48%, #814321);
  box-shadow: 0 13px 22px rgba(75, 45, 32, .24), inset 0 0 0 2px rgba(255, 245, 210, .25);
  color: #fff7dc;
  text-align: center;
}
.ticket-topline, .ticket-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  font-size: .66rem;
  font-weight: 700;
  letter-spacing: .08em;
}
.ticket-title {
  color: #fff2bd;
  font-size: clamp(2rem, 5vw, 3rem);
  font-weight: 900;
  line-height: .95;
  letter-spacing: .06em;
  text-shadow: 0 3px 0 #78401f, 0 5px 8px rgba(60, 30, 10, .25);
  white-space: pre-line;
}
.ticket-stars { color: #ffe7a2; font-size: 1.25rem; }
.ticket-bottom { font-size: .58rem; letter-spacing: .02em; }
.ticket-bottom strong { color: #fff2bd; font-size: 1rem; }
.scratch-result {
  display: flex;
  justify-content: center;
  gap: 12px;
  padding: 7px 0;
  color: #fff4c9;
  font-size: 2rem;
  text-shadow: 0 2px 2px rgba(60, 30, 10, .35);
}
.scratch-foil {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
  border: 1px solid rgba(255,255,255,.55);
  background:
    radial-gradient(circle at 25% 30%, rgba(255,255,255,.8), transparent 18%),
    repeating-linear-gradient(135deg, #d8d5cc 0 5px, #aaa69b 5px 10px, #eeeae0 10px 15px);
  box-shadow: inset 0 0 18px rgba(70, 60, 45, .25);
  color: #655e52;
  cursor: default;
}
.scratch-foil.is-scratching { animation: scratch-away 1.35s cubic-bezier(.55,.05,.8,.4) forwards; }
.scratch-foil.is-revealed { opacity: 0; pointer-events: none; }
.foil-label { padding: 7px 15px; border: 2px dashed rgba(90,80,65,.55); border-radius: 5px; font-size: .9rem; font-weight: 900; letter-spacing: .16em; }
.foil-stars { font-size: 1.4rem; letter-spacing: .25em; }
@keyframes scratch-away {
  0% {
    clip-path: polygon(0 0, 100% 0, 100% 18%, 100% 36%, 100% 54%, 100% 72%, 100% 100%, 0 100%, 0 72%, 0 36%);
    opacity: 1;
    transform: rotate(var(--scratch-angle)) scale(1);
  }
  18% {
    clip-path: polygon(0 0, 78% 0, 86% 14%, 72% 27%, 88% 43%, 70% 59%, 83% 78%, 0 100%, 12% 70%, 0 35%);
  }
  42% {
    clip-path: polygon(0 0, 53% 0, 63% 13%, 47% 29%, 66% 45%, 48% 62%, 61% 82%, 0 100%, 10% 69%, 0 34%);
  }
  68% {
    clip-path: polygon(0 0, 27% 0, 39% 15%, 22% 31%, 42% 47%, 24% 64%, 36% 84%, 0 100%, 9% 70%, 0 33%);
  }
  100% {
    clip-path: polygon(0 0, 0 0, 3% 15%, 0 30%, 4% 47%, 0 64%, 3% 83%, 0 100%, 0 70%, 0 34%);
    opacity: 0;
    transform: rotate(var(--scratch-angle)) scale(1.025);
  }
}
.ticket-level a.is-selected { border-color: var(--caramel); background: var(--cream-deep); }
@media (prefers-reduced-motion: reduce) {
  .scratch-foil.is-scratching { animation-duration: .01ms; }
}
</style>
