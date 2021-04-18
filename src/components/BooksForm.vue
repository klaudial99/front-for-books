<template>
    <div id="books-form">
        <form @submit.prevent="handleSubmit">

            <div class="row">

                <div class="col-5">
                    <div class="form-group">
                        <label for="author-form">Autor:</label>

                        <select multiple 
                            class="form-control" 
                            id="author-form"
                            v-model="book.authors" 
                            type="number"
                            options="authorsSource"
                            :class="{ 'has-error': submitting && invalidAuthor}"
                            @focus="clearStatus"
                            @keypress="clearStatus"
                        >
                            <option 
                                v-for="author in authorsSource" :key="author.id"
                                :value="author.id"
                            >{{ author.firstName + ' ' + author.lastName }}</option>
                        </select>
                    </div>
                </div>

                <div class="col-7">

                    <div class="row">

                        <div class="col">
                            <div class="form-group">
                                <label for="title_form">Tytuł</label>
                                <input 
                                    v-model="book.title" 
                                    type="text"
                                    class="form-control"
                                    id="title_form"
                                    :class="{ 'has-error': submitting && invalidTitle}"
                                    @focus="clearStatus"
                                    @keypress="clearStatus" 
                                />
                            </div>
                        </div>


                    </div>

                    <div class="row">

                        <div class="col-9">

                            <div class="form-group">
                                <label for="pages_form">Liczba stron</label>
                                <input 
                                    v-model="book.pages" 
                                    type="number"
                                    class="form-control"
                                    id="pages_form"
                                    :class="{ 'has-error': submitting && invalidPages}"
                                    @focus="clearStatus"
                                    @keypress="clearStatus" 
                                />
                            </div>
                        </div>

                        <div class="col-3">
                            <button class="btn btn-success" id="confirm">Zatwierdź</button>
                        </div>

                    </div>
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
    name: 'books-form',
        props: {
        authorsSource: Array,
    },
    data() {
        return {
            submitting: false,
            error: false,
            success: false,
            book: {
                authors: [],
                title: '',
                pages: '',
            },

        }
    },
    methods: {
        handleSubmit() {
            this.submitting = true
            this.clearStatus()

            //check from fields
            if (this.invalidAuthor || this.invalidTitle || this.invalidPages) {
                this.error = true
                return
            }

            this.book.authors = $('#author-form').val().map(Number)
            this.book.pages = parseInt(this.book.pages)
            this.$emit('add:book', this.book)

            this.book = {
                authors: [],
                title: '',
                pages: '',
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
        invalidAuthor() {
            return this.book.authors === []
        },

        invalidTitle() {
            return this.book.title === ''
        },

        invalidPages() {
            return this.book.pages === ''
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