<template>
  <div class="main-container">
    <div class="message-generator">
      <h2 class="text-2xl font-bold mb-4">AI Message Generator</h2>

 
      <textarea
        v-model="prompt"
        placeholder="Enter your prompt (e.g. Diwali wishes for customers)"
        class="w-full border rounded p-2 mb-4"
        rows="3"
      ></textarea>

   
      <button
        @click="generateMessage"
        class="bg-green-600 text-white px-4 py-2 rounded hover:bg-green-700 flex items-center justify-center"
        :disabled="loading"
      >
        <span v-if="!loading">Generate Message</span>
        <span v-else class="loader"></span>
      </button>

      <div v-if="message" class="mt-6">
        <h3 class="text-xl font-semibold mb-2">Generated Message</h3>
        <textarea
          v-model="message"
          class="w-full border rounded p-2"
          rows="4"
        ></textarea>
        <p class="text-sm text-gray-500 mt-2">
          You can edit the message before sending.
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AiMessageGenerator",
  data() {
    return {
      prompt: "",
      message: "",
      loading: false, 
    };
  },
  methods: {
    async generateMessage() {
      if (!this.prompt.trim()) {
        alert("Please enter a prompt!");
        return;
      }

      this.loading = true; 
      this.message = ""; 

      try {
        const API_KEY = "Your API KEY"; 
        const trimmedMessage = this.prompt.trim();

        const response = await fetch(
          `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${API_KEY}`,
          {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({
              contents: [
                {
                  parts: [
                    {
                      text: `Generate a customer-friendly message for: ${trimmedMessage}`,
                    },
                  ],
                },
              ],
            }),
          }
        );

        const data = await response.json();

        this.message =
          data?.candidates?.[0]?.content?.parts?.[0]?.text ||
          "Sorry, could not generate message.";
      } catch (error) {
        console.error("Error generating message:", error);
        this.message = "⚠️ Failed to generate message. Check API key or network.";
      } finally {
        this.loading = false; 
      }
    },
  },
};
</script>

<style scoped>
.main-container {
  display: flex;
  justify-content: flex-end;
  padding: 20px;
}

.message-generator {
  width: 85%;
  max-width: 1225px;
  background: #ffffff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

button {
  transition: background 0.2s;
  position: relative;
}

textarea {
  font-size: 14px;
  resize: vertical;
}


.loader {
  border: 3px solid #f3f3f3;
  border-top: 3px solid white;
  border-radius: 50%;
  width: 18px;
  height: 18px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
