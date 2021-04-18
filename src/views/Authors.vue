<template>
  <div id="app" class="container">
    

    <div class="row justify-content-center">
      <div class="col-10">
        <h1>Autorzy</h1>
        <authors-form :booksSource="books" @add:author="addAuthor" />
      </div>
    </div>
    

    <div class="row justify-content-center">
      <div class="col-10">
        <authors-table :booksSource="books" :authorsSource="authors" @delete:author="deleteAuthor" @edit:author="editAuthor" />
      </div>
    </div>
    
  </div>
</template>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
import AuthorsTable from '@/components/AuthorsTable.vue'
import AuthorsForm from '@/components/AuthorsForm.vue'

export default {
  name: 'app',
  components: {
    AuthorsTable,
    AuthorsForm
  },
  data() {

    return {
      books: [],
      authors: []
    }
  },
  methods: {
    addAuthor(author) {
      try {
        fetch('http://localhost:9000/author', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(author)
        })
      } catch (error) {
        console.log(error)
      }
      this.getAuthors() //?
      // this.setName(book)
      // this.books.push(book)

    },

    deleteAuthor(id) {
      try {
        fetch(`http://localhost:9000/author/${id}`, {
          method: 'DELETE',
        })

      } catch (error) {
        console.log(error)
      }
      var removeIndex = this.authors.map(function(author) { 
        return author.id;
         }).indexOf(id);
      this.authors.splice(removeIndex, 1)
    },

    editAuthor(author) {
      try {
        fetch(`http://localhost:9000/author/${author.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(author)
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
    setBooks(author) {
      var vbooks = []
      author.books.forEach((b) => {
        this.books.forEach((bu)=>{
          if (bu.id === b)
            vbooks.push({
              id : bu.id, 
              title : bu.title, 
              pages : bu.pages
            })
        }) 
      })
      author.books = vbooks
    },
    async getAuthors() {
      try {
        const response = await fetch('http://localhost:9000/get/books')
        const data = await response.json()

        const response2 = await fetch('http://localhost:9000/get/authors')
        const data2 = await response2.json()

        this.books = data
        this.books.forEach((e)=> {
          this.setName(e)
        })
        this.authors = data2
        this.authors.forEach((e)=> {
          this.setBooks(e)
        })
      } catch (error) {
        console.log(error)
      }
    }
  },

  mounted() {
    this.getAuthors()
  }
}
</script>

<style>

</style>
