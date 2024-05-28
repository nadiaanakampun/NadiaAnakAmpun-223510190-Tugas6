<template>
  <div>
    <form @submit.prevent="save" class="form-container">
      <input type="text" v-model="form.title" placeholder="Title" class="form-input" /><br />
      <textarea v-model="form.content" placeholder="Content" class="form-input"></textarea><br />
      <button type="submit">{{ form.id ? 'Update' : 'Save' }}</button>
    </form>
    <ul>
      <li v-for="article in articles" :key="article.id">
        <div class="article">
          <h3>{{ article.title }}</h3>
          <p>{{ article.content }}</p>
          <button @click="edit(article)">Edit</button>
          <button @click="remove(article.id)">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load">Load</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      }
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        // Update existing article
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
        }
      } else {
        // Add new article
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load(); // Load articles when component is mounted
  }
};
</script>