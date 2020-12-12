<template>
  <div id="app">
    <main class="news-search py-4">
      <b-container fluid>
        <b-row class="my-1">
          <b-col lg="4">
            <label class="h4">Search for the latest news:</label>
          </b-col>
          <b-col lg="8">
            <b-form-input id="search" type="search" placeholder="Search..." v-model="searchRequest"></b-form-input>
          </b-col>
        </b-row>

        <b-row class="mt-1 mb-3">
          <b-col lg="2" sm="3">
            <b-form-group
                id="fieldset-1"
                label="News per page:"
                label-for="pageSizeSelect"
            >
              <b-form-select id="pageSizeSelect" v-model="perPage" :options="pageSizeOptions" size="sm"></b-form-select>
            </b-form-group>
          </b-col>
        </b-row>

        <b-card v-for="article in news.articles" :key="article.url" no-body class="overflow-hidden my-3">
          <b-row no-gutters>
            <b-col md="4">
              <b-card-img :src="article.urlToImage" alt="Image" class="rounded-0 snippet__image"></b-card-img>
            </b-col>
            <b-col md="8">
              <b-card-body :title="article.title">
                <b-card-text class="small text-muted">
                  {{ article.publishedAt.slice(0, 10) }} at
                  {{ article.publishedAt.slice(11, 16) }}{{ article.author ? `, by ${article.author}` : '' }}
                </b-card-text>
                <b-card-text>{{ article.description }}</b-card-text>
                <b-button :href="article.url" variant="primary" type="button">Read more</b-button>
              </b-card-body>
            </b-col>
          </b-row>
        </b-card>

        <b-pagination
            class="my-4"
            align="center"
            v-model="currentPage"
            :total-rows="news.totalResults"
            :per-page="perPage"
            first-text="First"
            prev-text="Prev"
            next-text="Next"
            last-text="Last">
        </b-pagination>
      </b-container>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      news: {},
      pageSizeOptions: [5, 10, 20],
      perPage: 10,
      currentPage: 1,
      searchRequest: '',
    }
  },
  mounted() {
    this.loadNews()
  },
  watch: {
    perPage: function () {
      this.loadNews()
    },
    currentPage: function () {
      this.loadNews()
    },
    searchRequest: function () {
      this.loadNews()
      this.currentPage = 1
    },
  },
  methods: {
    async loadNews() {
      this.news = await fetch('http://newsapi.org/v2/top-headlines'
          + `?country=us`
          + `&apiKey=4503e6cdf4a34e0a8c870451f7abba6c`
          + `&pageSize=${this.perPage}`
          + `&page=${this.currentPage}`
          + `&q=${this.searchRequest}`
      )
          .then(res => res.json())
    }
  },
}
</script>

<style>
.news-search {
  max-width: 992px;
  margin: auto;
}

.snippet__image {
  object-fit: cover;
  height: 280px;
}
</style>
