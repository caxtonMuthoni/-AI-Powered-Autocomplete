<script>
import { DOMDirectiveTransforms } from "@vue/compiler-dom";
import { Configuration, OpenAIApi } from "openai"
export default {

  data() {
    return {
      question: "",
      aiResponse: [],
      heading: "",
      loading: false
    }
  },


  methods: {
    async chatGpt(question) {
      console.log(import.meta.env)
      const configuration = new Configuration({
        apiKey: import.meta.env.VITE_OPENAI_API_KEY,
      });
      const openai = new OpenAIApi(configuration);
      const response = await openai.createCompletion({
        model: "text-davinci-003",
        prompt: question,
        temperature: 0,
        max_tokens: 1000,
      });
      // console.log(response.data.);
      this.aiResponse = response.data.choices;
      this.heading = question
    },

    async submitRequest() {
      try {
        this.loading = true
        await this.chatGpt(this.question)
      } catch (error) {
        console.log(error);
      } finally {
        this.loading = false
      }
    }

  },

}
</script>

<template>
  <div class="gpt">
    <div class="form-container">
      <h1>Generate Text Instantly with AI-Powered Autocomplete</h1>
      <div class="form">
        <textarea class="text-area" v-model="question" rows="5" placeholder="Ask me anything ..."></textarea>
      </div>
      <button :disabled="loading" class="btn" @click="submitRequest">
        <span v-if="loading">Loading ...</span>
        <span v-else>Submit</span>
      </button>
    </div>
    <div v-if="aiResponse.length > 0" class="answer-container">
      <h2>{{ heading }}</h2>
      <p v-for="(choice, i) in aiResponse" :key="i" v-html="choice.text"></p>
    </div>
  </div>
</template>

<style scoped>

</style>
