<template>
    <div class="max-w-2xl mx-auto">
      <h2 class="text-2xl font-semibold mb-4">Farm Analysis Input</h2>
      
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="form-group">
          <label>Language:</label>
          <select v-model="formData.language" class="form-select">
            <option value="English">English</option>
            <option value="Urdu">Urdu</option>
            <option value="Punjabi">Punjabi</option>
            <option value="Sindhi">Sindhi</option>
          </select>
        </div>
  
        <div class="form-group">
          <label>Silage Type:</label>
          <select v-model="formData.silageType" class="form-select">
            <option value="Custom">Custom</option>
            <option value="Corn Silage">Corn Silage</option>
            <option value="Grass Silage">Grass Silage</option>
            <option value="Alfalfa Silage">Alfalfa Silage</option>
            <option value="Wheat Silage">Wheat Silage</option>
          </select>
        </div>
  
        <div class="form-group">
          <label>Protein Content (%):</label>
          <input type="range" v-model="formData.proteinContent" min="0" max="100" step="0.1" class="form-range">
          <span>{{ formData.proteinContent }}%</span>
        </div>
  
        <div class="form-group">
          <label>Fiber Content (%):</label>
          <input type="number" v-model="formData.fiberContent" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Energy Content:</label>
          <input type="number" v-model="formData.energyContent" step="0.01" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Body Condition Score:</label>
          <input type="number" v-model="formData.bodyConditionScore" step="0.1" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Somatic Cell Count:</label>
          <input type="number" v-model="formData.somaticCellCount" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Temperature (°C):</label>
          <input type="number" v-model="formData.temperature" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Humidity (%):</label>
          <input type="number" v-model="formData.humidity" class="form-input">
        </div>
  
        <div class="form-group">
          <label>Number of Cows:</label>
          <input type="number" v-model="formData.numCows" class="form-input">
        </div>
      </div>
  
      <button @click="getPrediction" class="btn btn-primary mt-4">Get Prediction</button>
  
      <div v-if="prediction" class="mt-6">
        <h3 class="text-xl font-semibold mb-2">Prediction Result:</h3>
        <pre class="bg-gray-100 p-4 rounded">{{ formattedPrediction }}</pre>
        <button @click="proceedToPayment" class="btn btn-success mt-4">Proceed to Payment</button>
      </div>
    </div>
  </template>
  
  <script>
  import { Client } from "@gradio/client";
  
  export default {
    name: 'FarmAnalysisForm',
    data() {
      return {
        formData: {
          language: 'English',
          silageType: 'Custom',
          proteinContent: 16.5,
          fiberContent: 20,
          energyContent: 1.65,
          bodyConditionScore: 3.5,
          somaticCellCount: 150000,
          temperature: 22,
          humidity: 60,
          numCows: 200,
          question: 'What is the prediction?' // Add a default question
        },
        prediction: null
      };
    },
    computed: {
      formattedPrediction() {
        if (Array.isArray(this.prediction)) {
          return this.prediction.filter(item => item !== null).join('\n\n');
        }
        return this.prediction;
      }
    },
    methods: {
      async getPrediction() {
        try {
          const client = await Client.connect("poemsforaphrodite/farms");
          const result = await client.predict("/get_prediction_and_suggestions", {
            language: this.formData.language,
            silage_type: this.formData.silageType,
            protein_content: this.formData.proteinContent,
            fiber_content: this.formData.fiberContent,
            energy_content: this.formData.energyContent,
            body_condition_score: this.formData.bodyConditionScore,
            somatic_cell_count: this.formData.somaticCellCount,
            temperature: this.formData.temperature,
            humidity: this.formData.humidity,
            num_cows: this.formData.numCows,
            question: this.formData.question // Include the question parameter
          });
  
          this.prediction = result.data;
        } catch (error) {
          console.error('Error getting prediction:', error);
          alert('Failed to get prediction. Please try again.');
        }
      },
      proceedToPayment() {
        const form = document.createElement('form');
        form.method = 'POST';
        form.action = 'https://sandbox.payfast.co.za/eng/process';
  
        const addInput = (name, value) => {
          const input = document.createElement('input');
          input.type = 'hidden';
          input.name = name;
          input.value = value;
          form.appendChild(input);
        };
  
        addInput('merchant_id', '22134');
        addInput('merchant_key', 'VT9_U4RM6Fi-JAM3mG-yiWVT3');
        addInput('amount', '100.00');  // Set your desired amount
        addInput('item_name', 'Farm Analysis');
        addInput('return_url', window.location.href);
        addInput('cancel_url', window.location.href);
  
        document.body.appendChild(form);
        form.submit();
  
        // Simulate payment completion (remove this in production)
        setTimeout(() => {
          this.$emit('payment-complete');
        }, 5000);
      }
    }
  };
  </script>
  
  <style scoped>
  /* Add your styles here */
  </style>