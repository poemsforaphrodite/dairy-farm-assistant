<template>
  <div id="app" class="container mx-auto p-4">
    <h1 class="text-3xl font-bold mb-6 text-center">Kissan Analytics</h1>
    
    <farm-analysis-form v-if="!paid" @payment-complete="onPaymentComplete" />

    <div v-if="paid" class="max-w-2xl mx-auto">
      <h2 class="text-2xl font-semibold mb-4">Chat with AI Assistant</h2>
      <div class="chat-container bg-gray-100 rounded p-4 mb-4">
        <div v-for="(message, index) in chatMessages" :key="index" :class="['message', message.type]">
          {{ message.text }}
        </div>
      </div>
      <div class="flex">
        <input v-model="userInput" @keyup.enter="sendMessage" placeholder="Type your message..." class="form-input flex-grow mr-2">
        <button @click="sendMessage" class="btn btn-primary">Send</button>
      </div>
    </div>
  </div>
</template>

<script>
import FarmAnalysisForm from './components/FarmAnalysisForm.vue';

export default {
  name: 'App',
  components: {
    FarmAnalysisForm
  },
  data() {
    return {
      paid: false,
      chatMessages: [],
      userInput: ''
    };
  },
  methods: {
    onPaymentComplete() {
      this.paid = true;
    },
    sendMessage() {
      if (this.userInput.trim() === '') return;

      this.chatMessages.push({ type: 'user', text: this.userInput });
      this.userInput = '';

      // Simulate AI response (replace with actual LLM integration)
      setTimeout(() => {
        this.chatMessages.push({ type: 'ai', text: 'This is a simulated AI response. Integrate with your preferred LLM here.' });
      }, 1000);
    }
  }
};
</script>