<template>
    <form @submit.prevent="submitQuote" class="form-box">
      <div class="form-group">
        <label for="quote" class="form-label">
          Your Coding Quote (max 3 lines with comma separated) *
        </label>
        <textarea
          id="quote"
          v-model="quote"
          class="form-input"
          rows="3"
          placeholder="Write your poetic lines, separated by commas"
          required
        ></textarea>
      </div>
  
      <div class="form-group">
        <label for="poet" class="form-label">
          Poet Name (optional)
        </label>
        <input
          id="poet"
          v-model="poet"
          class="form-input"
          placeholder="By default #poetic_coder"
        />
      </div>
  
      <button type="submit" :disabled="isLoading">
        {{ isLoading ? "Posting..." : "Post Quote" }}
      </button>
  
      <!-- Loader -->
      <p v-if="isLoading" class="loader">
        <span class="spinner"></span> Posting your quote...
      </p>
  
      <!-- Status Message -->
      <p v-if="statusMessage" class="status-message">{{ statusMessage }}</p>
  
      <!-- Image -->
      <img
        v-if="imageUrl"
        :src="imageUrl"
        alt="Generated Quote"
        class="preview-img"
      />
    </form>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const quote = ref('')
  const poet = ref('')
  const imageUrl = ref('')
  const statusMessage = ref('')
  const isLoading = ref(false)
  
  const submitQuote = async () => {
    isLoading.value = true
    statusMessage.value = ''
    imageUrl.value = ''
  
    const parts = quote.value.split(',').map(part => part.trim())
    const line1 = parts[0] || ''
    const line2 = parts[1] || ''
    const line3 = parts[2] || ''
  
    const payload = {
      line1,
      line2,
      line3,
      author: poet.value || 'poetic_coder'
    }
  
    try {
      const res = await fetch('https://follower-can-post.onrender.com/poetry', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      })
  
      const data = await res.json()
      imageUrl.value = data.image_url
      statusMessage.value = data.status || 'Posted successfully!'
      quote.value = ''
      poet.value = ''
    } catch (err) {
      statusMessage.value = '❌ Error posting quote. Try again later.'
      imageUrl.value = ''
      console.error(err)
    } finally {
      isLoading.value = false
    }
  }
  </script>
  
  <style scoped>
  .form-box {
    background: rgba(255, 255, 255, 0.9);
    border-radius: 12px;
    padding: 1.5rem;
    max-width: 600px;
    margin: 2rem auto;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
  }
  
  .form-group {
    margin-bottom: 1rem;
  }
  
  .form-label {
    font-weight: bold;
    color: #111;
    margin-bottom: 0.5rem;
    display: block;
  }
  
  .form-input {
    width: 100%;
    padding: 0.6rem;
    border: 1px solid #ccc;
    border-radius: 6px;
    font-family: inherit;
    font-size: 1rem;
  }
  
  button {
    width: 100%;
    padding: 0.75rem;
    background-color: #2563eb;
    color: white;
    border: none;
    border-radius: 6px;
    font-weight: bold;
    font-size: 1rem;
    cursor: pointer;
    transition: background 0.3s;
  }
  
  button:hover:enabled {
    background-color: #1e40af;
  }
  
  button:disabled {
    background-color: #93c5fd;
    cursor: not-allowed;
  }
  
  .loader {
    text-align: center;
    font-weight: bold;
    color: #2563eb;
    margin-top: 1rem;
  }
  
  .spinner {
    display: inline-block;
    width: 16px;
    height: 16px;
    border: 2px solid #2563eb;
    border-top: 2px solid transparent;
    border-radius: 50%;
    animation: spin 0.6s linear infinite;
    vertical-align: middle;
    margin-right: 0.5rem;
  }
  
  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }
  
  .status-message {
    margin-top: 1rem;
    text-align: center;
    color: green;
    font-weight: 500;
  }
  
  .preview-img {
    margin-top: 1.5rem;
    max-width: 100%;
    border-radius: 10px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  }
  </style>
  