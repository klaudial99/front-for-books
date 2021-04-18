<template>
  <div id="app" class="container">
    

    <div class="row justify-content-center">
      <div class="col-10">
        <h1>Książki</h1>
        <books-form :authorsSource="authors" @add:book="addBook" />
      </div>
    </div>
    

    <div class="row justify-content-center">
      <div class="col-10">
        <books-table :booksSource="books" :authorsSource="authors" @delete:book="deleteBook" @edit:book="editBook" />
      </div>
    </div>
    
  </div>
</template>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
import BooksTable from '@/components/BooksTable.vue'
import BooksForm from './components/BooksForm.vue'

export default {
  name: 'app',
  components: {
    BooksTable,
    BooksForm
  },
  data() {

    return {
      books: [],
      authors: []
    }
  },
  methods: {
    addBook(book) {
      //this.books = [...this.books, book]
      try {
        fetch('http://localhost:9000/book', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(book)
        })
      } catch (error) {
        console.log(error)
      }
      this.getBooks()
      // this.setName(book)
      // this.books.push(book)

    },

    deleteBook(id) {
      try {
        fetch(`http://localhost:9000/book/${id}`, {
          method: 'DELETE',
        })

      } catch (error) {
        console.log(error)
      }
      var removeIndex = this.books.map(function(book) { 
        return book.id;
         }).indexOf(id);
      this.books.splice(removeIndex, 1)
    },

    editBook(book) {
      try {
        fetch(`http://localhost:9000/book/${book.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(book)
        })

      } catch (error) {
        console.log(error)
      }
      location.reload();
    },

    setName(book) {
      var auth = []
      book.authors.forEach((a) => {
        this.authors.forEach((au)=>{
          if (au.id === a)
            auth.push({
              id : au.id, 
              firstName : au.firstName, 
              lastName : au.lastName
            })
        }) 
      })
      book.authors = auth
    },

    async getBooks() {
      try {
        const response = await fetch('http://localhost:9000/get/books')
        const data = await response.json()
        this.books = data

        this.books.forEach((e)=> {
          this.setName(e)
        })
      } catch (error) {
        console.log(error)
      }
    },

    async getAuthors() {
      try {
        const response = await fetch('http://localhost:9000/get/authors')
        const data = await response.json()
        this.authors = data

      } catch (error) {
        console.log(error)
      }
    }
  },

  mounted() {
    this.getAuthors()
    this.getBooks()
  }
}
</script>

<style>

</style>
