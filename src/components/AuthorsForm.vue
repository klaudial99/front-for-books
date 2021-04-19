<template>
    <div id="authors-form">
        <form @submit.prevent="handleSubmit">

            <div class="row">
                <div class="col-6">
                    <div class="form-group">
                        <label for="firstName_form">Imię:</label>
                        <input 
                            v-model="author.firstName" 
                            type="text"
                            class="form-control"
                            id="firstName_form"
                            :class="{ 'has-error': submitting && invalidFirstName}"
                            @focus="clearStatus"
                            @keypress="clearStatus" 
                        />
                    </div>

                    <div class="form-group">
                        <label for="lastName_form">Nazwisko:</label>
                        <input 
                            v-model="author.lastName" 
                            type="text"
                            class="form-control"
                            id="lastName_form"
                            :class="{ 'has-error': submitting && invalidLasName}"
                            @focus="clearStatus"
                            @keypress="clearStatus" 
                        />
                    </div>
        
                </div>

                <div class="col-6">
                    <div class="form-group">
                        <label for="book-form">Książki:</label>

                        <select multiple 
                            class="form-control" 
                            id="book-form"
                            v-model="author.books" 
                            type="number"
                            options="booksSource"
                            @focus="clearStatus"
                            @keypress="clearStatus"
                        >
                            <option 
                                v-for="book in booksSource" :key="book.id"
                                :value="book.id"
                            >{{ book.title }}</option>
                        </select>
                    </div>

                    <button class="btn btn-success" id="confirm">Zatwierdź</button>

                </div>
            </div>

            <p v-if="error && submitting" class="error-message">
                Proszę wypełnić wskazane pola formularza
            </p>
            <p v-if="success" class="success-message">
                Dane poprawnie zapisano
            </p>    

        </form>
    </div>
    
</template>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>

export default {
    name: 'authors-form',
        props: {
        booksSource: Array,
    },
    data() {
        return {
            submitting: false,
            error: false,
            success: false,
            author: {
                books: [],
                firstName: '',
                lastName: '',
            },

        }
    },
    methods: {
        handleSubmit() {
            this.submitting = true
            this.clearStatus()

            //check from fields
            if (this.invalidFirstName || this.invalidLasName) {
                this.error = true
                return
            }

            this.author.books = $('#book-form').val().map(Number)
            this.$emit('add:author', this.author)

            this.author = {
                books: [],
                firstName: '',
                lastName: '',
            }
            this.error = false
            this.success = true
            this.submitting = false
        },

        

        clearStatus() {
            this.success = false
            this.error = false
        }
    },
    computed: {
        invalidFirstName() {
            return this.author.firstName === ''
        },

        invalidLasName() {
            return this.author.lastName === ''
        }
    }
}
</script>

<style scoped>
    form {
        margin-bottom: 2rem;
    }

    [class*='-message'] {
        font-weight: 500;
    }

    .error-message {
        color: #D33C40;
    }

    .success-message {
        color: #32A95D;
    }

    #confirm {
        float: right;
        margin-top: 2em;
    }
</style>