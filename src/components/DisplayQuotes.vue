<template> 
  <div class="quotes-container">
    <div class="buttons">
      <button @click="generateNewQuote">Generate New Quote</button>
      <button @click="copyQuoteToClipboard">Copy Quote</button>
    </div>

    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <div v-else class="quote">
      <h3>Quote:</h3>
      "{{ quote.content }}" <br> <strong>{{ quote.author }}</strong>
    </div>

    <p v-if="copySuccess" class="copy-success">Quote copied to clipboard!</p>

    <div v-if="quoteHistory.length" class="quote-history">
      <h3>History:</h3>
      <ul>
        <li v-for="(item, index) in quoteHistory" :key="index">
          "{{ item.content }}" <br> <strong>{{ item.author }}</strong>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      quote: {
        content: '',
        author: ''
      },
      errorMessage: '',
      copySuccess: false,
      quoteHistory: []
    };
  },

  methods: {
    async fetchQuote() {
      this.errorMessage = '';
      try {
        const response = await axios.get('https://api.quotable.io/random');
        this.quote = response.data;
        this.copySuccess = false;
      } catch (error) {
        this.errorMessage = 'Failed to fetch a quote. Please try again.';
      }
    },
async generateNewQuote() {
  const currentQuote = { ...this.quote };
  this.addQuoteToHistory(currentQuote);
  await this.fetchQuote();
}
,
    async copyQuoteToClipboard() {
      try {
        const quoteText = `"${this.quote.content}" — ${this.quote.author}`;
        await navigator.clipboard.writeText(quoteText);
        this.copySuccess = true;
        setTimeout(() => (this.copySuccess = false), 2000);
      } catch (error) {
        this.errorMessage = 'Failed to copy the quote. Please try again.';
      }
    },
    addQuoteToHistory(newQuote) {
      this.quoteHistory.unshift({ content: newQuote.content, author: newQuote.author });
    }
  },
  async mounted() {
    await this.fetchQuote();
  }
};
</script>

<style scoped>
.quotes-container {
  margin: 7% 30%;
  display: flex;
  flex-direction: column;
  font-size: 1.2em;
}

.quote {
  background-color: antiquewhite;
  border-radius: 8px;
  padding: 0 8px 26px;
  margin-top: 20px;
}

.error {
  color: red;
}

.buttons {
  display: flex;
  gap: 3%;
}

button {
  padding: 10px 20px;
  margin: 10px 0;
  width: 100%;
  border-radius: 10px;
  border: 1px solid #97ddff;
  background-color: #d0f1fb;
  font-weight: 700;
}

.copy-success {
  color: green;
  margin-top: 10px;
}

.quote-history {
  border-radius: 8px;
  padding: 0 8px 26px;
  margin-top: 20px;
  border: 1px solid antiquewhite;
}

.quote-history h3 {
  font-size: 1.1em;
}

.quote-history ul {
  padding-left: 20px;
}

.quote-history li {
  margin-bottom: 15px;
}
</style>
