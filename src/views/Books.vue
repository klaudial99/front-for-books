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
        <books-table :authorsSource="authors" :booksSource="books" @delete:book="deleteBook" @edit:book="editBook" />
      </div>
    </div>
    
  </div>
</template>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
import BooksTable from '@/components/BooksTable.vue'
import BooksForm from '@/components/BooksForm.vue'

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

    // for each book: [id] -> [{id, first, last}]
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

        const response = await fetch('http://localhost:9000/get/authors')
        const data = await response.json()

        const response2 = await fetch('http://localhost:9000/get/books')
        const data2 = await response2.json()

        this.authors = data
        this.books = data2

        this.books.forEach((e)=> {
          this.setName(e)
        })
      } catch (error) {
        console.log(error)
      }
    },
  },

  mounted() {
    this.getBooks()
  }
}
</script>

<style>

</style>
