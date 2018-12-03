<template>
  <div>
    <h3>已读书单</h3>
    <div class="stat">
      {{ books.length }}本/{{ days }}天
      <i class="fas" :class="sortClass" @click="toggle"></i>
    </div>
    <div class="books">
      <div v-for="(book, i) in books" :key="i" class="book">
        <h5>{{ book.title }}</h5>
        <div class="date">
          {{ book.date }}
        </div>
        <star-rating
          v-model="book.rating"
          :star-size="10"
          :max-rating="10"
          :read-only="true"
          :increment="0.1"/>
        <div class="comment" v-html="toHtml(book.comment)"></div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import StarRating from 'vue-star-rating'

export default {
  components: {
    StarRating
  },
  data () {
    return {
      books: [],
      sortClass: 'fa-sort-amount-down'
    }
  },
  mounted () {
    this.getBooks()
  },
  computed: {
    days () {
      if (!this.books.length) {
        return 0
      }
      const [y, m, d] = this.firstDate.split('-').map(it => +it)
      return ~~((new Date() - new Date(y, m - 1, d)) /
        (24 * 60 * 60 * 1000)) + 1
    }
  },
  methods: {
    getBooks () {
      axios.get('data.json?d=' + new Date()).then(res => {
        this.books = res.data
        this.firstDate = res.data[res.data.length - 1].date
      })
    },
    toHtml (text) {
      return text.replace(/\n/g, '<br/>')
    },
    toggle () {
      this.books = this.books.reverse()
      this.sortClass = this.sortClass === 'fa-sort-amount-down' ?
        'fa-sort-amount-up' : 'fa-sort-amount-down'
    }
  }
}
</script>

<style>
body {
  font-size: 14px;
}
h3 {
  text-align: center;
  font-size: 18px;
  padding: 6px;
  margin: 0;
}
.stat {
  background: #efefef;
  padding: 5px;
}
.fas {
  float: right;
  font-size: 18px;
  margin-right: 5px;
}
.books {
  padding: 0 6px;
}
.book {
  border-bottom: 1px solid #eee;
  padding: 6px 0;
}
h5 {
  font-size: 16px;
  padding: 6px 0;
  margin: 0;
}
.date {
  color: #666;
}
</style>
