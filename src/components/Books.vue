<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="search">
        <select name="" id="" v-on:change="booksInList">
          <option value="" selected disabled >Choose a category</option>
          <option v-bind:value="category.list_name_encoded" v-for="category in categories" :key='category.list_name_encoded'>
            {{ category.display_name }}
          </option>
        </select>
        <input type="text" class="form-control" placeholder="Search by title" v-on:keydown="searchBook" />
      </div>
      <div class="content">
        <div v-for="book in searchedBook" :key="book.title" class="book" v-on:click="googleSearch">
          <div class="image">
            <img v-bind:src="book.book_image"/>
          </div>
          <h1 class="bookTitle">{{ book.title }}</h1>
          <h3 class="bookAuthor">{{ book.author }}</h3>
          <p class="bookDescription">{{ book.description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: "Books",
  data(){
    return {
      categories: [],
      books: [],
      api_key: 'aZJUSY3hvGpAAAKUEXLh9wUuNrbpxcNh',
      searchedBook: []
    };
  },  
  mounted(){
    this.getLists();
  },
  methods: {
    getLists(){
      axios.get(`https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=${this.api_key}`) 
        .then(response => {

          for(let i = 0; i < 10; i++){
            this.categories.push(response.data.results[i]);
          }

        })       
    },
    booksInList: function(){
      let select = document.querySelector('select');

      axios.get( `https://api.nytimes.com/svc/books/v3/lists/${select.value}.json?api-key=${this.api_key}` )
        .then( response => {
          this.books = response.data.results.books;
        })
        
    },
    searchBook: function(event){
      let select = document.querySelector('select');
      let input = document.querySelector('input');

      if(event.key == 'Enter'){
        
        if(select.value == ''){
  
          alert('You have to choose a category in order to search for a book');
  
        } else if(event.target.value.length < 2){
  
          alert('You have to write at least 2 characters');
  
        }else{
          
          let bookTitle = input.value.toUpperCase();
  
          this.searchedBook = this.books.filter( (val) => {
            if( val.title.includes(bookTitle) ){
              return val
            }
          });
        }
      }

    },
    googleSearch: function(event){
      let title = event.currentTarget.querySelector('.bookTitle').innerHTML;
      let author = event.currentTarget.querySelector('.bookAuthor').innerHTML;
      let search = title+' '+author;

      search = search.replace(/ /g, '+');
      location.href = `https://www.google.com/search?q=${search}`;
    }
    
  }
};
</script>

<style scoped lang="scss">
  @import '../styles/books.scss' 
</style>
