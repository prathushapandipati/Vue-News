<template>
  <div>
    <b-jumbotron class="text-center pb-2"  bg-variant="dark" header="SwedenNews" text-variant="white">
      
      <br>
      <!--<b-alert variant="danger" show dismissible fade>
        Due to changes in the pricing policy of the News API, API calls can only be made locally. Please clone the repository and run it locally.
      </b-alert>-->
      <b-container>
        <b-form @submit.prevent="fetchData()">
          <b-form-input
            align = "left"
            class="mx-auto mt-5 w-50"
            type="search"
            placeholder="Search"
            v-model="search"
          />
        </b-form>
      </b-container>
    </b-jumbotron>

    <b-container>
      
      <b-row>
        <b-col
          cols="6"
          class="d-flex align-items-stretch"
          v-for="article in articles"
          :key="article.id"
          @click="openLink(article.url)"
        >
          <b-card
            :title="article.title"
            :img-src="article.urlToImage"
            class="mb-5"
          >
            <b-card-text>{{ article.description }}</b-card-text>
            <b-card-text class="text-muted">{{ article.author }}</b-card-text>
            <b-card-text class="text-muted bottom">{{
              article.publishedAt | formatDate
            }}</b-card-text>
          </b-card>
        </b-col>
      </b-row>
      <b-pagination
        pills
        align="right"
        v-model="currentPage"
        :total-rows="totalResults"
        :per-page="newsPerPage"
        class="mt-5 mb-5"
        @input="updatePage(currentPage)"
      />
    </b-container>
  </div>
</template>

<script>
import moment from 'moment'

export default {
  name: 'News',
  props: ['apiKey'],
  data: () => {
    return {
      url: '',
      currentPage: 1,
      totalResults: 0,
      newsPerPage: 10,
      articles: [],
      search: '',
    }
  },
  filters: {
    formatDate: function(value) {
      if (value) {
        return moment(String(value)).format('MM/DD/YYYY hh:mm')
      }
    },
  },
  methods: {
    fetchData() {
      if (this.search !== '') {
        this.url =
          'https://newsapi.org/v2/everything?q=' +
          this.search +
          '&pageSize=' +
          this.newsPerPage +
          '&apiKey=' +
          this.apiKey
      } else {
        this.url =
          'https://newsapi.org/v2/top-headlines?country=se&pageSize=' +
          this.newsPerPage +
          '&apiKey=' +
          this.apiKey

        this.search = ''
      }

      this.articles = []

      let request = new Request(this.url + '&page=' + this.currentPage)
      fetch(request)
        .then((response) => response.json())
        .then((data) => {
          this.totalResults = data.totalResults
          data.articles.forEach((element) => {
            this.articles.push(element)
          })
        })
        .catch((error) => {
          console.log(error)
        })
    },

    updateNewsPerPage(value) {
      this.newsPerPage = value
      this.currentPage = 1
      this.fetchData()
    },

    updatePage(page) {
      this.currentPage = page
      this.fetchData()
    },

    openLink(url) {
      window.open(url)
    },
  },
  created() {
    this.fetchData()
  },
}
</script>

<style scoped>
img {
  height: 250px;
  width: 100%;
  object-fit: cover;
}
.bottom {
  position: absolute;
  right: 10px;
  bottom: 10px;
}
.card {
  border-radius: 4px;
  background: #fff;
  box-shadow: 0 6px 10px rgba(0, 0, 0, 0.08), 0 0 6px rgba(0, 0, 0, 0.05);
  transition: 0.3s transform cubic-bezier(0.155, 1.105, 0.295, 1.12),
    0.3s box-shadow,
    0.3s -webkit-transform cubic-bezier(0.155, 1.105, 0.295, 1.12);
  cursor: pointer;
}

.card:hover {
  transform: scale(1.05);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.12), 0 4px 8px rgba(0, 0, 0, 0.06);
}
</style>
