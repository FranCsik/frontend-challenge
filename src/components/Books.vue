<template>
  <div class="container">
    <div class="card">
      <div class="heading">
        <h4 class="title">NYTimes Books</h4>
      </div>
      <div class="search">
        <select name="" id="">
          <option value="" selected disabled >Choose a category</option>
          <option v-bind:value="category.list_name_encoded" v-for="category in categories" :key='category.list_name_encoded'>
            {{ category.display_name }}
          </option>
        </select>
        <input type="text" class="form-control" placeholder="Search by title" v-on:keydown="searchBook" />
      </div>
      <div class="content">
        <div v-for="book in books" :key="book.title" class="book" v-on:click="googleSearch">
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
  data(){
    return {
      categories: [],
      books: [],
      api_key: 'aZJUSY3hvGpAAAKUEXLh9wUuNrbpxcNh'
    };
  },  
  mounted(){
    this.getBooks();
  },
  methods: {
    getBooks(){
      axios.get(`https://api.nytimes.com/svc/books/v3/lists/names.json?api-key=${this.api_key}`) 
        .then(response => {
          for(let i = 0; i < 10; i++){
            this.categories.push(response.data.results[i])
          }
        })       
    },
    searchBook: function(event){
      let select = document.querySelector('select');
      let input = document.querySelector('input');

      if(event.key == 'Enter' && select.value == ''){
        alert('You have to choose a category in order to search for a book')
      }else if(event.key == 'Enter'){
        let bookTitle = input.value.replace(/ /g, '+');

        axios.get(`https://api.nytimes.com/svc/books/v3/lists/best-sellers/history.json?title=${bookTitle}&api-key=${this.api_key}`) 
        .then(response => {
          if(response.data.num_results != 0){
            //si ya se hicieron busquedas, se borran de books
            this.books = [];
            for(let i = 0; i < response.data.results.length; i++){
              this.books.push(response.data.results[i])
            }
          }else{
            this.books = []
            alert(`We coudn't find any book with with the following title:${input.value}. Please try again with another book title :)` )
          }
        })
      }
    },
    googleSearch: function(event){
      let title = event.currentTarget.querySelector('.bookTitle').innerHTML;
      let author = event.currentTarget.querySelector('.bookAuthor').innerHTML;
      let search = title+' '+author;
      search = search.replace(/ /g, '+');
      location.href = `https://www.google.com/search?q=${search}`
    }
    
  },
  name: "Books"
};
</script>

<style scoped lang="scss">
.container {
  z-index: 1;
  margin: 36px auto;
  max-width: 826px;
  background-color: white;
}

.card {
  box-shadow: 0 2px 3px rgba(10, 10, 10, 0.1), 0 0 0 1px rgba(10, 10, 10, 0.1);
  padding: 24px;
}

.list {
  > li {
    &:not(:last-child) {
      margin-bottom: 18px;
    }
    > a {
      color: #0a5b8c;
      display: block;
      margin-bottom: 6px;
    }

    > span {
      color: rgba(#3b4242, 0.7);
      font-size: 12px;
    }
  }
}

.btn {
  color: #fff;
  cursor: pointer;
  background-color: #117a8b;
  border: 1px solid transparent;
  padding: 6px 12px;
  border-radius: 6px;
  transition: all 0.1s ease-in;
  &:hover {
    background-color: #138496;
    border-color: #117a8b;
  }
}

.heading {
  margin-bottom: 12px;

  .title {
    font-size: 18px;
    font-weight: 600;
  }
}
.search {
  margin-bottom: 24px;
  .form-control {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #ced4da;
    border-radius: 0;
    outline: 0;
    box-shadow: none;
    padding: 6px 0;
    width: 100%;
  }
}
.book {
  margin-top:20px;
  padding: 10px;
  border: 1px solid black;
}
.bookTitle{
  font-size: 25px;
  font-weight: 700;
}
.bookAuthor{
  font-size: 20px;
  margin-top: 5px;
}
.bookDescription{
  font-size: 16px;
  margin-top: 3px;
}

@media(min-width: 768px){
  .content{
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
    margin: auto;
  }
  .book{
    width: 300px;
    height: 250px;
    min-width: 300px;
    cursor: pointer;
  }
}

@media(min-width: 1024px){
  
}

</style>
