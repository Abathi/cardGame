<template>
  <div class="card-game">
    <h1>Card Game</h1>
    <div v-if="winnerPlayer !== null">
      <h2>Winner Player: {{ winnerPlayer + 1 }}</h2>
    </div>
    <div class="cards">
      <h2>Shuffled Cards:</h2>
      <div class="card-grid">
        <div v-for="(card, index) in shuffledCards" :key="index">
          {{ card }}
        </div>
      </div>
    </div>
    <div class="players-cards">
      <h2>Players' Cards:</h2>
      <div v-for="(player, index) in 4" :key="index">
        <h3>Player {{ index + 1 }}:</h3>
        <div class="card-grid">
          <div
            v-for="card in shuffledCards.slice(index * 13, (index + 1) * 13)"
            :key="card"
          >
            {{ card }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cards: [
        "2@",
        "2#",
        "2^",
        "2*",
        "3@",
        "3#",
        "3^",
        "3*",
        "4@",
        "4#",
        "4^",
        "4*",
        "5@",
        "5#",
        "5^",
        "5*",
        "6@",
        "6#",
        "6^",
        "6*",
        "7@",
        "7#",
        "7^",
        "7*",
        "8@",
        "8#",
        "8^",
        "8*",
        "9@",
        "9#",
        "9^",
        "9*",
        "10@",
        "10#",
        "10^",
        "10*",
        "J@",
        "J#",
        "J^",
        "J*",
        "Q@",
        "Q#",
        "Q^",
        "Q*",
        "K@",
        "K#",
        "K^",
        "K*",
        "A@",
        "A#",
        "A^",
        "A*",
      ],
      shuffledCards: [],
      cardCounts: Array.from({ length: 4 }, () => ({})),
      winnerPlayer: null,
    };
  },
  methods: {
    shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },
    findCardWithMaxCount(counts) {
      let maxCount = 0;
      let maxAlphanumeric = "";
      const alphanumericValues = Object.keys(counts);

      for (const alphanumeric of alphanumericValues) {
        const count = counts[alphanumeric];

        if (
          count > maxCount ||
          (count === maxCount && alphanumeric > maxAlphanumeric)
        ) {
          maxCount = count;
          maxAlphanumeric = alphanumeric;
        }
      }
      return maxAlphanumeric;
    },
    findHighestCountAlphanumeric(counts) {
      let maxCount = 0;
      let maxAlphanumeric = "";

      for (const alphanumeric in counts) {
        if (counts[alphanumeric] > maxCount) {
          maxCount = counts[alphanumeric];
          maxAlphanumeric = alphanumeric;
        } else if (counts[alphanumeric] === maxCount) {
          // If counts are equal, compare alphanumeric values
          if (compareAlphanumeric(alphanumeric, maxAlphanumeric) > 0) {
            maxAlphanumeric = alphanumeric;
          }
        }
      }

      return maxAlphanumeric;
    },
    compareAlphanumeric(alpha1, alpha2) {
      const values = [
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "J",
        "Q",
        "K",
        "A",
      ];
      return values.indexOf(alpha1) - values.indexOf(alpha2);
    },
    compareWinners(winners, cardCounts) {
      let winnerPlayer = winners[0];
      for (let i = 1; i < winners.length; i++) {
        const currentWinner = winners[i];
        const comparison = this.compareAlphanumeric(
          this.findHighestCountAlphanumeric(cardCounts[currentWinner]),
          this.findHighestCountAlphanumeric(cardCounts[winnerPlayer])
        );

        if (comparison > 0) {
          winnerPlayer = currentWinner;
        }
      }

      return winnerPlayer;
    },
  },
  created() {
    // Shuffling cards
    this.shuffledCards = this.shuffle(this.cards);

    // Distributing cards to players and determining who is the winner
    let winners = [];
    let maxCount = 0;

    const cardCounts = Array.from({ length: 4 }, () => ({}));

    for (let i = 0; i < 4; i++) {
      for (let j = 0; j < 13; j++) {
        const card = this.shuffledCards[i * 13 + j];

        // Checking if the card is valid
        if (!this.cards.includes(card)) {
          continue;
        }

        const alphanumericPart = card.slice(0, -1); // Removing the suit symbol

        // Updating counts while distributing
        cardCounts[i][alphanumericPart] =
          (cardCounts[i][alphanumericPart] || 0) + 1;
      }

      // Finding the max count
      const counts = Object.values(cardCounts[i]);
      const max = Math.max(...counts);

      if (max > maxCount) {
        winners = [i];
        maxCount = max;
      } else if (max === maxCount) {
        winners.push(i);
      }
    }

    this.winnerPlayer = this.compareWinners(winners, this.cardCounts);
  },
};
</script>
<style scoped>
.card-game {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.cards,
.players-cards {
  margin-top: 20px;
}

.card-grid {
  display: grid;
  gap: 10px;
}

.card-grid div {
  padding: 10px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  text-align: center;
}

/* Mobile view */
@media (max-width: 600px) {
  .card-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

/* Other views */
@media (min-width: 601px) {
  .card-grid {
    grid-template-columns: repeat(13, 1fr);
  }
}
</style>
