<template>

    <div id="books-table">
        <table class="table table-hover">
            <thead>
                <tr>
                    <th>Imię</th>
                    <th>Nazwisko</th>
                    <th></th>
                    <th></th>
                </tr>
            </thead>
            <tbody v-for="author in authorsSource" :key="author.id">

                <tr @click="showBooks('author'+author.id)">
                    <td>{{ author.firstName }}</td>
                    <td>{{ author.lastName }}</td>
                    <td></td>
                    <td>
                        <button type="button" class="btn btn-info btn-sm" :id="author.id" @click="handleEdit">Edytuj</button>
                        <button type="button" class="btn btn-danger btn-sm" @click="handleDelete(author.id)">Usuń</button>
                    </td>
                </tr>
                <tr :id="'author'+author.id" class="hidden-row no-hover" v-if="author.books.length != 0">
                    <td colspan="2" class="ml-2">
                        <table class="table table-sm subtable ml-2">
                            <thead>
                                <tr class="no-hover">
                                    <th>Tytuł</th>
                                    <th>Liczba stron</th>
                                </tr>
                            </thead>
                            <tbody v-for="book in author.books" :key="book.id">
                                <tr class="no-hover">
                                    <td>{{ book.title}}</td>
                                    <td>{{ book.pages}}</td>
                                </tr>
                            </tbody>

                        </table> 

                    </td>
                </tr>

            </tbody>
        </table>
    </div>
</template>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script>
export default {
    name: 'books-table',
    props: {
        booksSource: Array,
        authorsSource: Array
    },
    data() {
        return {
            submitting: false,
            error: false,
            success: false,
            isEdit: false,
            editedAuthor: {
                books: [],
                firstName: '',
                lastName: '',
            },
        }
    },
    methods: {

        showBooks(row) {
            if (!this.isEdit)
                $("#" + row).toggle();
        },

        changeButtons(row) {
            var $btn1 = row.children().eq(3).children().eq(0)
            var $btn2 = row.children().eq(3).children().eq(1)

            if(this.isEdit) {
                $btn1.html('Zapisz')
                $btn1.attr('class', 'btn btn-success btn-sm')
                $btn2.html('Anuluj')
            }
            else {
                $btn1.html('Edytuj')
                $btn1.attr('class', 'btn btn-info btn-sm')
                $btn2.html('Usuń')
            }
        },

        clearStatus() {
            this.success = false
            this.error = false
        },

        handleDelete(id) {
            if (this.isEdit)
            {
                location.reload();
            }
            else
            {
                this.$emit('delete:author', id)
            }
            
        },
        handleEdit: function(event) {

            this.isEdit = !this.isEdit
            
            var $authorId = parseInt(event.target.id)
            var $authorIndex = this.authorsSource.map(function(author) { 
                return author.id;
            }).indexOf($authorId);
            var $author = this.authorsSource[$authorIndex]

            var $grandParent = $('#'+$authorId).parent().parent(); //row
            var $firstName = $grandParent.children().eq(0)
            var $lastName = $grandParent.children().eq(1)
            var $books = $grandParent.children().eq(2)

            

            if (this.isEdit)
            {

                $('.btn-danger').not($grandParent.children().eq(3).children().eq(1)).prop('disabled', true);
                $('.btn-info').not($grandParent.children().eq(3).children().eq(0)).prop('disabled', true);

                $firstName.html(function() {
                    return '<input class="form-control" :class="{ "error": invalidFirstName}"/>'
                })
                $lastName.html(function() {
                    return '<input class="form-control" :class="{ "error": invalidLastName}"/>'
                })
                $books.html(function() {
                    return '<select multiple class="form-control" id="book-form" v-model="author.books" type="number" options="booksSource"></select>'
                })

                $.each(this.booksSource, function (i, item) {
                    $books.children().append($('<option>', { 
                        value: item.id,
                        text : item.title 
                    }));

                });
                $books.children().val($author.books.map(function(b) { 
                    return b.id;
                }))
                $grandParent.children().eq(0).children().val($author.firstName)
                $grandParent.children().eq(1).children().val($author.lastName)
            }
            else
            {

                this.submitting = true
                this.clearStatus()

                //check from fields
               


                
                this.editedAuthor = {
                    id: $author.id,
                    books: $books.children().val(),
                    firstName: $firstName.children().val(),
                    lastName: $lastName.children().val()
                }
                
                if (this.invalidFirstName || this.invalidLasName) {
                    this.error = true
                    this.isEdit = !this.isEdit

                    return
                }
                
                $('.btn-danger').prop('disabled', false);
                $('.btn-info').prop('disabled', false);

                this.$emit('edit:author', this.editedAuthor)

                this.error = false
                this.success = true
                this.submitting = false
            } 
            this.changeButtons($grandParent)
        },
        
    },
    computed: {
        invalidFirstName() {
            return this.editedAuthor.firstName === ''
        },

        invalidLasName() {
            return this.editedAuthor.lastName === ''
        }
    }
}
</script>

<style scoped>

    pre {
        margin: 0;
        padding: 0;
        font-family: inherit;
    }

    th {
        border-top: none;
    }

    .btn-sm {
        margin-left: 10px;
    }

    .hidden-row {
        display: none;
    }

    .no-hover:hover {
        background-color: #FFFFFF;
    }

    .error {
        background-color: red;
    }

</style>