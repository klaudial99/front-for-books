<template>

    <div id="books-table">
        <table class="table table-hover">
            <thead>
                <tr>
                    <th>Autor</th>
                    <th>Tytuł</th>
                    <th>Liczba stron</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="book in booksSource" :key="book.id">
                    <td >
                        <span v-for="author in book.authors" :key="author.id">
                            <pre>{{ author.firstName + ' ' + author.lastName + '' }}</pre>
                        </span>
                    </td>
                    <td>{{ book.title }}</td>
                    <td>{{ book.pages }}</td>
                    <td>
                        <button type="button" class="btn btn-info btn-sm" :id="book.id" @click="handleEdit">Edytuj</button>
                        <button type="button" class="btn btn-danger btn-sm" @click="handleDelete(book.id)">Usuń</button>
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
            editedBook: {
                authors: [],
                title: '',
                pages: '',
            },
        }
    },
    methods: {
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
                this.$emit('delete:book', id)
            }
            
        },
        handleEdit: function(event) {

            this.isEdit = !this.isEdit
            
            var $bookId = parseInt(event.target.id)
            var $bookIndex = this.booksSource.map(function(book) { 
                return book.id;
            }).indexOf($bookId);
            var $book = this.booksSource[$bookIndex]

            var $grandParent = $('#'+$bookId).parent().parent();
            var $author = $grandParent.children().eq(0)
            var $title = $grandParent.children().eq(1)
            var $pages = $grandParent.children().eq(2)

            

            if (this.isEdit)
            {

                $('.btn-danger').not($grandParent.children().eq(3).children().eq(1)).prop('disabled', true);
                $('.btn-info').not($grandParent.children().eq(3).children().eq(0)).prop('disabled', true);
                $author.html(function() {
                    return '<select multiple class="form-control" id="author-form" v-model="book.authors" type="number" options="authorsSource"></select>'
                })
                $title.html(function() {
                    return '<input class="form-control"/>'
                })
                $pages.html(function() {
                    return '<input class="form-control"/>'
                })

                $.each(this.authorsSource, function (i, item) {
                    $author.children().append($('<option>', { 
                        value: item.id,
                        text : item.firstName + ' ' + item.lastName 
                    }));

                });
                $author.children().val($book.authors.map(function(a) { 
                    return a.id;
                }))
                $grandParent.children().eq(1).children().val($book.title)
                $grandParent.children().eq(2).children().val($book.pages)
            }
            else
            {
                this.submitting = true
                this.clearStatus()

                
                this.editedBook = {
                    id: $book.id,
                    authors: $author.children().val(),
                    title: $title.children().val(),
                    pages: $pages.children().val()
                }

                if (this.invalidAuthor || this.invalidTitle || this.invalidPages) {
                    this.error = true
                    this.isEdit = !this.isEdit

                    return
                }

                $('.btn-danger').prop('disabled', false);
                $('.btn-info').prop('disabled', false);

                this.$emit('edit:book', this.editedBook)

                this.error = false
                this.success = true
                this.submitting = false
            } 

            this.changeButtons($grandParent)
        }
    },
    computed: {
        invalidAuthor() {
            return this.editedBook.authors === []
        },

        invalidTitle() {
            return this.editedBook.title === ''
        },

        invalidPages() {
            return this.editedBook.pages === ''
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
</style>